Storage Engine API
==================

ストレージエンジンAPIの目的は、MongoDBにおいてプラガブルなストレージエンジンを可能にすることです。
([Storage FAQ][]を参照)。このドキュメントはAPIの概要を示し、さらに詳細なドキュメントへのポインタを提供するものです。
コードを参照している箇所では、本ドキュメントが書かれた時点のバージョンのものを参照しています。
本ドキュメントに反映されていない変更があるかもしれないので、常に最新のバージョンと突き合わせて確認するようにしてください。
本ドキュメントに書かれていないAPIについての質問は、[mongodb-dev][]のGoogleグループを使ってください。
ストレージエンジンAPIに関わっている全員があなたの投稿を読むことになります。

サードパーティ製のストレージエンジンは、既存のMongoDBソースツリーに投入できる自己完結型モジュールを使用して統合され、自動的に設定されインクルードされます。典型的なモジュールは、少なくとも次のファイルを含んでいます。

    src/             実際のソースファイルを格納するディレクトリ
    README.md        ストレージエンジンに特有の情報
    SConscript       Sconsビルドルール
    build.py         モジュールの設定スクリプト

構成の例としては<https://github.com/mongodb-partners/mongo-rocks>を参照するとよいでしょう。


概念
--------

### レコードストア (Record Stores)
1つのデータベースは1つ以上のコレクションを保持していて、コレクションは複数のインデックスを保持しており、それらの一覧表を持っています。
全てのMongoDBのコレクションはレコードストアを使って実装されています。
ドキュメントそのものに対して1つのレコードストア、インデックスに対して1つのレコードストアが使われます。
KVEngineクラスを使うことで抽象化されたインターフェースのみを扱えばよいことになります。
KVStorageEngineは、カタログやインデックスのためにレコードストアを使ってStorageEngineインターフェースを実装しているからです。

#### レコードアイデンティティ
RecordIdは、ある時点において特定のドキュメントまたはレコードストア内のエントリに対し、
ストレージエンジンによって割り当てられるユニークな識別子です。
KVEngineに基づくストレージエンジンではレコードアイデンティティは固定ですが、
他のストレージエンジン、たとえばMMAPv1等では、ドキュメントを更新するときに変更される可能性があります。
インデックスがRecordIdにマップされるため、レコードアイデンティティを変更することは非常にコストが高くなる可能性があることに注意してください。
大きな配列をもつ1つのドキュメントは何千ものインデックスエントリを持つ可能性があり、更新のコストが非常に高くなる可能性があります。

#### クローニングとバルクオペレーション (Cloning and bulk operations)
現在、全てのクローニング、[初期同期][]、およびその他のオペレーションは個々のドキュメント単位で動作しますが、
より効率的にインデックスを構築するためにBulkBuilderクラスが存在します。

### ロックと同時実行 (Locking and Concurrency)
MongoDBは複数の粒度のインテントロックを使用します。[同時実行に関するFAQ][]を参照してください。
いずれの場合も、これによって、レコードストアの作成や削除のようなメタデータに対するオペレーションが他のアクセスに対して直列化されることが保証されます。
ストレージエンジンはドキュメントレベルの同時実行をサポートすることも可能です。その場合は、ストレージエンジンは、必要となる追加の同期処理を行う責任を負います。
ドキュメントレベルの同時実行をサポートしないストレージエンジンでは、MongoDBはコレクションレベルで共有／排他的ロックを使用します。したがってすべてのレコードストアのアクセスは直列化されます。

MongoDBは、管理しているリソースに対するアクセスの直列化可能性を保証するために、[2相ロック][] (2PL) を使います。
ドキュメントレベルの同時実行をサポートしているストレージエンジンについては、MongoDBはほとんどの一般的なオペレーションに対してはインテントロックのみを用います。ただしレコードストアレイヤーの同期処理は例外で、ストレージエンジンに任されます。

### トランザクション
各オペレーションは、ストレージエンジンによって実装された新しいRecoveryUnitを持つOperationContextを作成します。そのOperationContextはオペレーションが完了するまで生存します。
現在、クライアントにカーソルを返すクエリオペレーションは、クライアントカーソルと同じ期間生存します。OperationContextはそれ自身のRecoveryUnitとクライアントカーソルのRecoveryUnitとの間で切り替えを行います。いくつかの他のケースでは、内部コマンドはさらなるRecoveryUnitも使用するかもしれません。RecoveryUnitは、後述のようにトランザクションセマンティクスを実装する必要があります。

#### 原子性 (Atomicity)
書き込みは明示的にコミットされた時に限り、見えるようにならなければなりません。
そしてその場合すべての保留中の書き込みは、原子的に見えるようにならなければなりません。
作業単位(unit of work)が完了するまでにコミットされなかった書き込みは、ロールバックされなければなりません。
ストレージAPIを通して直接的になされた書き込みに加えて、ドキュメントの更新やレコードストアの作成、その他の変更も、リカバリユニットに登録される可能性があります。

#### 一貫性 (Consistency)
ストレージエンジンは全てのレコードストアにまたがって原子性と隔離性を保証しなければなりません。
そうでなければあるドキュメントとインデックスに対する原子的な更新は保証できなくなってしまいます。

#### 隔離性 (Isolation)
ストレージエンジンは、ロック (MMAPv1エンジンのように)、またはMulti Version Concurrency Control (MVCC) 、またはその他の方法によって、Snapshot Isolationを提供しなければなりません。
最初の読み込みで暗黙的にスナップショットが確立されます。オペレーションは常にRecoveryUnitのcontext内で自分が行ったすべての変更を見ることができます。しかし他のオペレーションからは、コミットが正常に完了するまでその変更を見ることはできません。

#### 耐久性 (Durability)
ひとたびトランザクションがコミットされたとしても、必ずしも永続化されるわけではありません。電源の喪失やその他の原因によりサーバーが故障した場合 (その場合に限りますが) 、データベースは過去のある時点まで戻る可能性があります。
しかしながら、トランザクションの原子性は維持されている必要があります。同様にレプリカセットでは、一旦利用不能となったプライマリがレプリカセットに再参加するとき、変更が過半数のノードから可視になっていなかった場合、過去の状態までロールバックされる必要があるかもしれません。
RecoveryUnitは、あるオペレーションがコミット済みトランザクションの永続化を待つことができるようにするためのメソッドを実装しています。

トランザクションは、コミットされればすぐに他のトランザクションから見えるようになります。ストレージエンジンは耐久性を実現するため、複数のトランザクションをバンドルしてグループコミットを使うかもしれません。
またその代わりに、ストレージエンジンはコミット時に永続化されるまで待っても構いません。

### Write Conflicts
Systems with optimistic concurrency control (OCC) or multi-version concurrency control (MVCC) may
find that a transaction conflicts with other transactions, that executing an operation would result
in deadlock or violate other resource constraints. In such cases the storage engine may throw a
WriteConflictException to signal the transient failure. MongoDB will handle the exception, abort
and restart the transaction.

### Point-in-time snapshot reads
Two functions on the RecoveryUnit help storage engines implement point-in-time reads: setTimestamp()
and selectSnapshot().  setTimestamp() is used by write transactions to label any forthcoming writes
with a timestamp; these timestamps are then used to produce a point-in-time read transaction via a
call to selectSnapshot() at the start of the read.  The storage engine must produce the effect of
reading from a snapshot that includes only writes with timestamps at or earlier than the
selectSnapshot timestamp.  This means that a point-in-time read may slice across prior write
transactions by hiding only some data from a given write transaction, if that transaction had a
different timestamp set prior to each write it did.

実装すべきクラス
--------------------

ストレージエンジンは一般に以下のクラスを実装する必要があります。詳細はこれらの実際の定義を参照してください。

* [KVEngine](kv/kv_engine.h)
* [RecordStore](record_store.h)
* [RecoveryUnit](recovery_unit.h)
* [SeekableRecordCursor](record_store.h)
* [SortedDataInterface](sorted_data_interface.h)
* [ServerStatusSection](../commands/server_status.h)
* [ServerParameter](../server_parameters.h)


[同時実行に関するFAQ]: http://docs.mongodb.org/manual/faq/concurrency/
[初期同期]: http://docs.mongodb.org/manual/core/replica-set-sync/#replica-set-initial-sync
[mongodb-dev]: https://groups.google.com/forum/#!forum/mongodb-dev
[replica set]: http://docs.mongodb.org/manual/replication/
[Storage FAQ]: http://docs.mongodb.org/manual/faq/storage
[2相ロック]: http://en.wikipedia.org/wiki/Two-phase_locking
