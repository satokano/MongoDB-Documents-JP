<!-- あとでQiita に投稿する https://qiita.com/kabao/items/ -->
<!-- マニュアルっぽいので「ですます」で -->
<!-- 他のマニュアルを参照する場合、どちらかというと英語タイトルそのままで。文章の一部になっていて、訳さないと変な場合は日本語に訳す。 -->
<!-- NOTE: とかの囲みは、QiitaのMDで対応するものがなさそうなので、注意：brなどとしてごまかす-->
<!-- SEE ALSO: は参照：で -->
<!-- 英単語・数字の周りに空白をいれるか？ -->
[Release Notes for MongoDB 3.6](https://docs.mongodb.com/master/release-notes/3.6/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

脚注は、リリースノート以外の3.6のマニュアルも参考にして今回付け加えたものです。

------------------------------

MongoDB 3.6は現在開発中です。

## セキュリティ
### デフォルトでlocalhostにのみバインド
MongoDB 3.6から、MongoDBのバイナリ（mongodおよびmongos）は、デフォルトではlocalhostにのみバインドされるようになりました。以前はMongoDB 2.6以来、公式のMongoDB RPM（Red Hat, CentOS, Fedora Linux、およびその派生）、DEB（Debian, Ubuntu、およびその派生）のみがlocalhostにバインドするようになっていました。詳細は[Localhost Binding Compatibility Changes](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#bind-ip-compatibility)を参照してください。

### 認証の制限
データベースに対するユーザーの接続を、特定のIPアドレスからのみに制限するため、authenticationRestrictions パラメータが以下のコマンドやメソッドに対して追加されました。

| Commands | Methods |
|:-----------|:------------|
| [createUser](https://docs.mongodb.com/master/reference/command/createUser/#dbcmd.createUser) | [db.createUser()](https://docs.mongodb.com/master/reference/method/db.createUser/#db.createUser) |
| [updateUser](https://docs.mongodb.com/master/reference/command/updateUser/#dbcmd.updateUser) | [db.updateUser()](https://docs.mongodb.com/master/reference/method/db.updateUser/#db.updateUser) |
| [createRole](https://docs.mongodb.com/master/reference/command/createRole/#dbcmd.createRole) | [db.createRole()](https://docs.mongodb.com/master/reference/method/db.createRole/#db.createRole) |
| [updateRole](https://docs.mongodb.com/master/reference/command/updateRole/#dbcmd.updateRole) | [db.updateRole()](https://docs.mongodb.com/master/reference/method/db.updateRole/#db.updateRole) |

### 追加のセキュリティ改善
- TLS/SSL暗号化を使うときのOpenSSLの暗号アルゴリズムを制御するため、opensslCipherConfigパラメータが追加されました。
- 認証が有効な場合に限り、作成したカーソルに対しgetMoreを発行できます。
- restoreロールに対してconvertToCappedアクションが追加されました。

参照：<br />
[後方非互換な機能](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#compatibility-enabled)

## Aggregation
MongoDB 3.6からは以下の機能が利用可能です。

### より表現的な ``$lookup``
$lookup は複数の結合条件および相関サブクエリが指定可能になりました。これは結合されたコレクションに対して変数の指定とパイプライン実行を許可することで可能になったものです。

[詳細は結合条件と相関サブクエリ](https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#lookup-syntax-let-pipeline)の[$lookup](https://docs.mongodb.com/master/reference/operator/aggregation/lookup/#pipe._S_lookup)シンタックスを参照してください。

### 新しいAggregationステージ

### 新しいAggregationオペレータ

### 新しいAggregationヘルパー

### 新しいAggregation変数

### 新しいオプション

### タイムゾーンのサポート

## 配列に対するupdateオペレータの改善

### ``arrayFilters``

### Multi-Element Array Updates

### Negative Array Index Position for ``push``

## Change Streams

## クライアントセッション
### 因果一貫性（Causal Consistensy）[^1]
因果一貫性を提供するため、MongoDB 3.6ではクライアントセッションにおいて[causal consistency]を有効にしています。因果一貫なクライアントセッションは、関連付けられた読み込みおよび''ack済みの''書き込みの一連のオペレーションが因果関係を持つ、つまり順序通り反映されることを示します。クライアントアプリケーションは、一度に1つのスレッドだけがクライアントセッションでこれらの操作を実行するようにする必要があります。

アプリケーションはクライアントセッションを開始し、特定のセッションにオペレーションを関連付けることができます。アプリケーションは一度に1つのスレッドだけがクライアントセッションでこれらの操作を実行するようにする必要があります。[^2]

重要：<br />
クライアントセッションを使うためには：<br />
- クライアントには、3.6用に更新されたドライバが必要です。Java、C#、Python、Node、Cなど。
- featureCompatibilityVersionは"3.6"である必要があります。詳細は[FeatureCompatibilityVersionを確認する方法](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv)もしくは[setFeatureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion)を参照してください。

### リトライ可能な書き込み
重要：<br />
リトライ可能な書き込みを使うためには：<br />
- クライアントには、3.6用に更新されたドライバが必要です。Java、C#、Python、Node、Cなど。
- featureCompatibilityVersionは"3.6"である必要があります。詳細は[FeatureCompatibilityVersionを確認する方法](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv)もしくは[setFeatureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion)を参照してください。

MongoDB 3.6 から、レプリカセットとシャードクラスタに対する特定のack済みオペレーションは、一時的なネットワーク障害やレプリカセットのマスタ選出を適切に処理するため「リトライ可能」となりました。

リトライ可能な書き込み機能により、MongoDBのドライバは、ネットワーク障害やレプリカセットのファイルオーバー（その間レプリカセットにはプライマリが存在しなくなる）に遭遇した場合、自動的にこれらのオペレーションをリトライします。3.6ドライバでリトライ可能な書き込みを有効にするには、[retryWrites](https://docs.mongodb.com/master/reference/connection-string/#urioption.retryWrites)を参照してください。

リトライはただ1回だけ試みられるので、リトライ可能機能は一時的なネットワーク障害にのみ対応可能で、永続的なネットワーク障害には対応できません。

リトライ可能な書き込みについて詳細は、[Retryable Writes](https://docs.mongodb.com/master/core/distributed-queries/#retryable-writes)を参照してください。

### mongo シェルの変更

## サーバセッション
### Server Session Commands

### Parameters

### Aggregation Stages

### General

### Command Options

## JSON Schema
MongoDB 3.6は、JSON Schemaを使ったドキュメントバリデーションをサポートするため $jsonSchema オペレータを追加しました。詳細は $jsonSchema を参照してください。

$jsonSchemaを使うには、featureCompatibilityVersionは"3.6"にセットされている必要があります。

参照：<br />
[後方非互換な機能](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#compatibility-enabled)

## レプリカセット
- レプリカセットのprotocol version 0（pv0）は非推奨となりました。レプリカセットのprotocol versionについて詳細は、[Replica Set Protocol Versions](https://docs.mongodb.com/master/reference/replica-set-protocol-versions/)を参照してください。
- replSetResizeOplogコマンドが追加され、レプリカセットのメンバーのoplogサイズを動的に変更できるようになりました。WiredTigerストレージエンジンを実行しているインスタンスにおいて利用可能です。
- catchUpTakeoverDelayMillis 設定オプションが追加され、
- pv1
- oplogInitialFindMaxSeconds
- waitForSecondaryBeforeNoopWriteMS

## シャードクラスタ
- mongosがmongodに対してのコネクションを追加する速度を制御するため、mongosにShardingTaskExecutorPoolMaxConnecting パラメータが追加されました。
- マイグレーションされたチャンクが元のシャードから削除されるまでの最小の時間を決める orphanCleanupDelaySecs が追加されました。
- configデータベースの中のconfig.system.sessions コレクションは、シャード化可能となりました。

## 一般的な改善
### MongoDB Compass のパッケージング
MongoDBサーバは、MongoDB Compass Community Editionの各プラットフォーム向けインストールスクリプトとともにパッケージ化されます。このスクリプトはMongoDBサーバのインストールプロセスの一環としてMongoDB Compassをインストールします。

### Collection Identifier

### New Query Operators

### Removed Operators

### Indexes

### Commands

### Wire Protocol と圧縮
- MongoDB 3.6では OP_MSG という新たなWire Protocolのopcodeが導入されました。このopcodeのメッセージフォーマットは拡張可能であり、他のopcodeの機能を包含するように設計されています。
- --networkMessageCompressorsオプション（または設定ファイル中のnet.compression.compressors）で利用するためのzlib圧縮が追加されました。--networkMessageCompressorsオプション（またはnet.compression.compressorsの設定）はmongod、mongos、mongoシェル、OP_COMPRESSEDメッセージフォーマットをサポートするドライバの間でのネットワーク圧縮を有効化します。
- snappyネットワーク圧縮がmongod、mongos、mongoシェルの間でデフォルトで有効になりました。

### Read Concern
- 新たに ["available"]()というRead Concernが導入されました。非シャード化コレクション（つまり、スタンドアロン環境か、レプリカセット環境）では、"local"と"available"のRead Concernは同じようにふるまいます。シャードクラスタでは、"available"はクラスタパーティションに対してより強い耐性を持ちますが、チャンクマイグレーション中のシャードはorphan documentsを返す可能性があります。

参照：<br />
[orphanCleanupDelaySecs](https://docs.mongodb.com/master/reference/parameters/#param.orphanCleanupDelaySecs)

- "majority" read concernが常に有効化されました。これに伴い、--enableMajorityReadConcern と replication.enableMajorityReadConcern は非推奨化されました。

### FTDC
MongoDB 3.6では、mongosでDiagnostics Capture（FTDCともいわれる）の出力が追加されました。以前のバージョンでは、この機能はmongodのみで利用可能でした。[Diagnostic Parameters](https://docs.mongodb.com/master/reference/parameters/#param-ftdc)を参照してください。

注意：<br />
FTDCはデフォルトで有効になっています。

### 追加の改善点
MongoDB 3.6は以下の改善を含みます。

- --bind_ip オプションにおいて、Unixドメインソケットの完全なパスを指定できるようになりました。
- mongod は新たに --timeZoneInfo オプションを指定できるようになりました。タイムゾーンデータを指定するために使ってください。LinuxとmacOS向けのデフォルト設定では、``/usr/share/zoneinfo``にセットされています。
- 日付に関するオペレーションは、サポートされているすべてのオペレーティングシステムにおいて、日付範囲を一貫して受け入れられるようになりました。0年から9999年までの範囲の年を安全に扱うことができます。
- mongodに対するhonorSystemUmaskという新しい起動時オプションにより、MongoDBが新規作成するファイルはmongodプロセスを実行するユーザのumaskで指定される読み込み/書き込み権限を持つようになります。LinuxおよびmacOSでのみ有効です。
- データベースに対する maxWriteBatchSize の制限が、1000 から 100000 に拡張されました。これはある1つのwrite batchの中で許可される書き込みオペレーションの最大数です。
- データベースコマンド planCacheListPlans はシェルでのPlanCache.getPlansByQuery() メソッドと同じものを出力します。両者からの出力は、プランが生成された時点のタイムスタンプを含むようになりました。

## 互換性への影響
いくつかの変更点は互換性に影響する可能性があり、ユーザーの対応が必要になるかもしれません。詳細な一覧は[MongoDB 3.6での互換性の変更点](https://docs.mongodb.com/master/release-notes/3.6-compatibility/)を参照してください。

## アップグレードの手順
注意：<br />
3.4のインスタンスをアップグレードするためには、3.4のインスタンスは``featureCompatibilityVersion``が3.4に設定されている必要があります。詳細は[FeatureCompatibilityVersionを確認する方法](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv)もしくは[setFeatureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion)を参照してください。

アップグレードの手順については、以下を参照してください。

* [スタンドアロン構成のサーバを3.6にアップグレードする](https://docs.mongodb.com/master/release-notes/3.6-upgrade-standalone/)
* [レプリカセットを3.4にアップグレードする](https://docs.mongodb.com/master/release-notes/3.6-upgrade-replica-set/)
* [シャードクラスタを3.4にアップグレードする](https://docs.mongodb.com/master/release-notes/3.6-upgrade-sharded-cluster/)

3.6へのアップグレードで支援が必要な場合は、[MongoDB社はメジャーバージョンアップグレードサービスを提供しています。](https://www.mongodb.com/products/consulting?jmp=docs)これは、あなたのアプリケーションを停止させることなく円滑な移行ができるように支援するものです。

## ダウンロード
MongoDB 3.6 Release Candidateをダウンロードするには、[MongoDB Download Center](https://www.mongodb.com/download-center?jmp=docs#development)を参照してください。

## 問題を報告する
問題を報告するためには https://github.com/mongodb/mongo/wiki/Submit-Bug-Reports を参照してください。MongoDBサーバや、関連プロジェクトについて、JIRAチケットを登録する方法が書いてあります。

------------------------------

[^1]: Causal Consistency https://en.wikipedia.org/wiki/Causal_consistency http://www.cs.princeton.edu/~wlloyd/papers/causal-login13.pdf

[^2]: 原文でも同じことを書いている。ミス？
