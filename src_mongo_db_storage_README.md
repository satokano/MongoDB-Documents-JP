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
Currently all cloning, [initial sync][] and other operations are done in terms of operating on
individual documents, though there is a BulkBuilder class for more efficiently building indexes.

### ロックと同時実行 (Locking and Concurrency)
MongoDB uses multi-granular intent locking; see the [Concurrency FAQ][]. In all cases, this will
ensure that operations to meta-data, such as creation and deletion of record stores, are serialized
with respect to other accesses. Storage engines can choose to support document-level concurrency,
in which case the storage engine is responsible for any additional synchronization necessary. For
storage engines not supporting document-level concurrency, MongoDB will use shared/exclusive locks
at the collection level, so all record store accesses will be serialized.

MongoDB uses [two-phase locking][] (2PL) to guarantee serializability of accesses to resources it
manages. For storage engines that support document level concurrency, MongoDB will only use intent
locks for the most common operations, leaving synchronization at the record store layer up to the
storage engine.

### トランザクション
Each operation creates an OperationContext with a new RecoveryUnit, implemented by the storage
engine, that lives until the operation finishes. Currently, query operations that return a cursor
to the client live as long as that client cursor, with the operation context switching between its
own recovery unit and that of the client cursor. In a few other cases an internal command may use
an extra recovery unit as well. The recovery unit must implement transaction semantics as described
below.

#### 原子性 (Atomicity)
書き込みは明示的にコミットされた時に限り、見えるようにならなければならない。
そしてその場合すべての保留中の書き込みは、原子的に見えるようにならなければならない。
作業単位(unit of work)が完了するまでにコミットされなかった書き込みは、ロールバックされなければならない。
ストレージAPIを通して直接的になされた書き込みに加えて、ドキュメントの更新やレコードストアの作成、
その他の変更も、リカバリユニットに登録される可能性がある。

#### 一貫性 (Consistency)
ストレージエンジンは全てのレコードストアにまたがって原子性と隔離性を保証しなければならない。
そうでなければあるドキュメントとインデックスに対する原子的な更新は保証できなくなってしまう。

#### 隔離性 (Isolation)
Storage engines must provide snapshot isolation, either through locking (as is the case for the
MMAPv1 engine), through multi-version concurrency control (MVCC) or otherwise. The first read
implicitly establishes the snapshot. Operations can always see all changes they make in the context
of a recovery unit, but other operations cannot until a successful commit.

#### 耐久性 (Durability)
Once a transaction is committed, it is not necessarily durable: if, and only if the server fails,
as result of power loss or otherwise, the database may recover to an earlier point in time.
However, atomicity of transactions must remain preserved. Similarly, in a replica set, a primary
that becomes unavailable may need to roll back to an earlier state when rejoining the replica set,
if its changes were not yet seen by a majority of nodes. The RecoveryUnit implements methods to
allow operations to wait for their committed transactions to become durable.

A transaction may become visible to other transactions as soon as it commits, and a storage engine
may use a group commit, bundling a number of transactions to achieve durability. Alternatively, a
storage engine may wait for durability at commit time.

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


[Concurrency FAQ]: http://docs.mongodb.org/manual/faq/concurrency/
[initial sync]: http://docs.mongodb.org/manual/core/replica-set-sync/#replica-set-initial-sync
[mongodb-dev]: https://groups.google.com/forum/#!forum/mongodb-dev
[replica set]: http://docs.mongodb.org/manual/replication/
[Storage FAQ]: http://docs.mongodb.org/manual/faq/storage
[two-phase locking]: http://en.wikipedia.org/wiki/Two-phase_locking
