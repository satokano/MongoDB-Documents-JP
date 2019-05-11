<!-- Qiita  -->
<!-- マニュアルっぽいので「ですます」で -->
<!-- 他のマニュアルを参照する場合、どちらかというと英語タイトルそのままで。文章の一部になっていて、訳さないと変な場合は日本語に訳す。 -->
<!-- NOTE: とかの囲みは、QiitaのMDで対応するものがなさそうなので、注意：brなどとしてごまかす？ ```text: かと思ったがそうすると中身のMD記法が解釈されない。divかと思ったがQiitaはstyle属性適用されない。divで囲んでタイトルはstrong、中身は自前でタグを書くのが見た目的には一番マシっぽい。 -->
<!-- SEE ALSO: は参照：、WARNING: は警告： -->
<!-- deprecated 非推奨 -->
<!-- 英単語・数字の周りに空白をいれるか？ -->
<!-- restの`、``と、Qiita Markdownの対応は？ -->
[Release Notes for MongoDB 4.2](https://docs.mongodb.com/master/release-notes/4.2/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

<!-- 脚注は、リリースノート以外の4.2のマニュアルなども参考にして今回付け加えたものです。 -->

現時点（2019/5）では4.2は未リリースで、4.1系として開発が続けられている状態です。リリースノートも今後正式リリースまでに変わる可能性があります。変更があった場合は可能な限り追随していく予定です。

------------------------------

# MongoDB 4.2 リリースノート

## シャードクラスタ環境での複数ドキュメントトランザクション
MongoDB 4.2では[複数ドキュメントトランザクション](https://docs.mongodb.com/master/core/transactions/)の機能がシャードクラスタ環境でも利用できるようになりました。

この機能を利用するには、シャードクラスタのすべてのメンバーにおいて [featureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv) が4.2になっている必要があります。

## MMAPv1ストレージエンジン廃止
MongoDB 4.2では、すでに非推奨であったMMAPv1ストレージエンジンが廃止になりました。

現在4.0の環境でMMAPv1を使用している場合は、MongoDB 4.2にアップグレードする前に、[WiredTigerストレージエンジン](https://docs.mongodb.com/master/core/wiredtiger/)に変更する必要があります。詳細については以下を参照してください。

- [スタンドアロン環境をWiredTigerに変更する](https://docs.mongodb.com/master/tutorial/change-standalone-wiredtiger/)
- [レプリカセットをWiredTigerに変更する](https://docs.mongodb.com/master/tutorial/change-replica-set-wiredtiger/)
- [シャードクラスタをWiredTigerに変更する](https://docs.mongodb.com/master/tutorial/change-sharded-cluster-wiredtiger/)

### MMAPv1特有の設定オプション

以下のMMAPv1特有の設定オプションは削除されました。

| Configuration File Setting | Command-line Option |
|:---------------------------|:--------------------|
| storage.mmapv1.journal.commitIntervalMs |  |
| storage.mmapv1.journal.debugFlags | mongod --journalOptions |
| storage.mmapv1.nsSize | mongod --nssize |
| storage.mmapv1.preallocDataFiles | mongod --noprealloc |
| storage.mmapv1.quota.enforced | mongod --quota |
| storage.mmapv1.smallFiles | mongod --smallfiles |
| storage.repairPath | mongod --repairpath |
| replication.secondaryIndexPrefetch | mongod --replIndexPrefetch |

### MMAPv1特有のパラメータ

以下のMMAPv1特有のパラメータは削除されました。

- newCollectionsUsePowerOf2Sizes
- replIndexPrefetch

### MMAPv1特有のコマンド

MMAPv1特有の touch コマンドは削除されました。

### ツール類、コマンド、メソッドに関するMMAPv1特有のオプション
MMAPv1特有の以下のオプションは削除されました。

- [collMod](https://docs.mongodb.com/master/reference/command/collMod/#dbcmd.collMod)に対するnoPaddingとusePowerOf2Sizes
- [collStats](https://docs.mongodb.com/master/reference/command/collStats/#dbcmd.collStats)に対するverbose
- [create](https://docs.mongodb.com/master/reference/command/create/#dbcmd.create)に対するflags
- [db.createCollection()](https://docs.mongodb.com/master/reference/method/db.createCollection/#db.createCollection)メソッドと[compact](https://docs.mongodb.com/master/reference/command/compact/#dbcmd.compact)コマンドに対するpaddingFactor、paddingBytes、preservePadding
- [mongodump](https://docs.mongodb.com/master/reference/program/mongodump/#bin.mongodump)に対するrepair

## 削除されたコマンドやメソッド

| Removed Command | Removed Method | Notes |
|:----------------|:---------------|:------|
| group | db.collection.group() | 代わりに[db.collection.aggregate()](https://docs.mongodb.com/master/reference/method/db.collection.aggregate/#db.collection.aggregate)で[$group](https://docs.mongodb.com/master/reference/operator/aggregation/group/#pipe._S_group)ステージを使用してください。 |
| eval |  | MongoDB 4.2の[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルでは、[db.eval()](https://docs.mongodb.com/master/reference/method/db.eval/#db.eval)と[db.collection.copyTo()](https://docs.mongodb.com/master/reference/method/db.collection.copyTo/#db.collection.copyTo)は、MongoDB 4.0もしくはそれ以前に対して接続されている時のみ実行できます。 |
| copydb |  | 対応する[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルのヘルパーであるdb.copyDatabase()は、MongoDB 4.0もしくはそれ以前に対して接続されている時のみ実行できます。<br />代替策として、[mongodump](https://docs.mongodb.com/master/reference/program/mongodump/#bin.mongodump)/[mongorestore](https://docs.mongodb.com/master/reference/program/mongorestore/#bin.mongorestore)や、カスタムスクリプトを用いることができます。 |
| clone |  | 対応する[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルのヘルパーであるdb.cloneDatabase()は、MongoDB 4.0もしくはそれ以前に対して接続されている時のみ実行できます。<br />代替策として、[mongodump](https://docs.mongodb.com/master/reference/program/mongodump/#bin.mongodump)/[mongorestore](https://docs.mongodb.com/master/reference/program/mongorestore/#bin.mongorestore)や、カスタムスクリプトを用いることができます。 |
| geoNear |  | 代わりに、db.collection.aggregate()において$geoNearステージを使用してください。<br />詳細については[geoNearコマンドの削除](https://docs.mongodb.com/master/release-notes/4.2-compatibility/#compat-remove-geonear)を参照してください。 |
| parallelCollectionScan |  |  |
| repairDatabase | db.repairDatabase() | 詳細については、[repairDatabaseコマンドの削除](https://docs.mongodb.com/master/release-notes/4.2-compatibility/#compat-remove-repairdatabase)を参照してください。 |

### maxScanオプションの削除

すでに非推奨であった、[find](https://docs.mongodb.com/master/reference/command/find/#dbcmd.find)コマンドのmaxScanオプションと、その[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルヘルパーであるcursor.maxScan()は削除されました。代わりに、[find](https://docs.mongodb.com/master/reference/command/find/#dbcmd.find)コマンドのmaxTimeMSオプションか、もしくはヘルパーの[cursor.maxTimeMS()](https://docs.mongodb.com/master/reference/method/cursor.maxTimeMS/#cursor.maxTimeMS)を使ってください。

## シャードクラスタ

複数シャードにまたがるトランザクションで原子性を維持するためには、[MongoDB Atlas](https://www.mongodb.com/cloud/atlas?jmp=docs)もしくは[MongoDB Ops Manager](https://www.mongodb.com/products/ops-manager?jmp=docs)で提供されるバックアップ・リストアを使用してください。これらのバックアップ・リストアはシャードクラスタ環境と適切に協調動作します。[手動のバックアップ・リストア](https://docs.mongodb.com/master/administration/backup-sharded-clusters/)では、複数シャードにまたがるトランザクションでの原子性を維持することができません。

## セキュリティに関する改善

### TLSオプションの追加

MongoDB 4.2では[mongod](https://docs.mongodb.com/master/reference/program/mongod/#tls-mongod-options)、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#mongos-tls-options)、[mongoシェル](https://docs.mongodb.com/master/reference/program/mongo/#mongo-shell-tls)において、従来のSSLオプションを置き換える、TLSオプションが追加されました。SSLオプションは4.2で非推奨となりました。MongoDBはTLS 1.0以降をずっとサポートしていたため、新しいTLSオプションは、SSLオプションと **同じ** 機能を提供します。

- コマンドラインTLSオプションについては、[mongod](https://docs.mongodb.com/master/reference/program/mongod/#tls-mongod-options)、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#mongos-tls-options)、[mongoシェル](https://docs.mongodb.com/master/reference/program/mongo/#mongo-shell-tls)の各ページを参照してください。
- 対応するmongodとmongosの設定ファイルオプションについては、[設定ファイル](https://docs.mongodb.com/master/reference/configuration-options/#net-tls-conf-options)のページを参照してください。
- 接続文字列におけるtlsオプションについては、[接続文字列](https://docs.mongodb.com/master/reference/connection-string/#uri-options-tls)のページを参照してください。

<div><strong>TIP：</strong><br />
ほとんどのTLSオプション名は、SSLオプション名と似ています。たとえば<a href="https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-tlsmode">--tlsMode</a>と<a href="https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-sslmode">--sslMode</a>というように。例外は以下です。
<ul>
<li>TLSでは<a href="https://docs.mongodb.com/master/reference/configuration-options/#net.tls.certificateKeyFile">net.tls.certificateKeyFile</a>、SSLでは<a href="https://docs.mongodb.com/master/reference/configuration-options/#net.ssl.PEMKeyFile">net.ssl.PEMKeyFile</a>
<li>TLSでは<a href="https://docs.mongodb.com/master/reference/configuration-options/#net.tls.certificateKeyFilePassword">net.tls.certificateKeyFilePassword</a>、SSLでは<a href="https://docs.mongodb.com/master/reference/configuration-options/#net.ssl.PEMKeyPassword">net.ssl.PEMKeyPassword</a>
<li>TLSでは<a href="https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-tlscertificatekeyfile">--tlsCertificateKeyFile</a>、SSLでは<a href="https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-sslpemkeyfile">--sslPEMKeyFile</a>
<li>TLSでは<a href="https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-tlscertificatekeyfile">--tlsCertificateKeyFilePassword</a>、SSLでは<a href="https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-sslpemkeypassword">--sslPEMKeyPassword</a>
</ul>
</div>

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/4.2/#tlsclustercafile">tlsClusterCAFileオプション</a></div>

### SSLオプションの非推奨化

MongoDB 4.2では[mongod](https://docs.mongodb.com/master/reference/program/mongod/#ssl-mongod-options)、[mongos](https://docs.mongodb.com/master/reference/program/mongos/#mongos-ssl-options)、[mongoシェル](https://docs.mongodb.com/master/reference/program/mongo/#mongo-shell-ssl)でのSSLオプション、それに対応する設定ファイルでの[net.sslオプション](https://docs.mongodb.com/master/reference/configuration-options/#net-ssl-conf-options)が非推奨となりました。

代わりに、新しい[TLS](https://docs.mongodb.com/master/release-notes/4.2/#tls)オプションを使用してください。

### tlsWithholdClientCertificateパラメータの追加

MongoDB 4.2では、[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)と[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)に対するsetParameterに[tlsWithholdClientCertificate](https://docs.mongodb.com/master/reference/parameters/#param.tlsWithholdClientCertificate)が追加されました。trueに設定すると、クラスタ内部認証の通信において当該mongodがクライアントとして振舞わないようになり、TLS証明書を他のmongodに対して送らないようになります。証明書無しでの着信を認める場合に使用してください。

### tlsClusterCAFileオプションの追加

MongoDB 4.2では、[--tlsClusterCAFile](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-tlsclustercafile)オプション、[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)と[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)での[net.tls.clusterCAFile](https://docs.mongodb.com/master/reference/configuration-options/#net.tls.clusterCAFile)オプションが追加されました。これらは.pemファイルを指定して、クライアントからのコネクション確立を受け付けるときにTLS証明書を検証するためのものです。これにより、TLSハンドシェイクのクライアント・サーバー間、サーバー・クライアント間のそれぞれを検証するのに別々の認証局を使用することが出来るようになりました。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/4.2/#tls">TLSオプション</a></div>

### passwordPrompt()

4.2以降の[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルでは、さまざまなユーザー認証、管理メソッド、コマンドと[passwordPrompt()](https://docs.mongodb.com/master/reference/method/passwordPrompt/#passwordPrompt)メソッドを組み合わせることで、パスワードをメソッドやコマンドに直接渡すのではなく、プロンプト入力させることができるようになりました。ただし、旧バージョンの[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルを使う場合はパスワードを直接渡す必要があることに注意してください。

以下に例を示します。

```
db.createUser( {
   user:"user123",
   pwd: passwordPrompt(),   // Instead of specifying the password in cleartext
   roles:[ "readWrite" ]
} )
```

### キーファイルがYAML形式に変更

MongoDB 4.2から、[クラスタ内部メンバーの認証](https://docs.mongodb.com/master/core/security-internal-authentication/#internal-auth-keyfile)に使用するキーファイルは、複数のキーを1つのキーファイルに格納できるようにするため、YAML形式となりました。YAML形式では以下の内容を含むことができます。

- 単一のキー文字列（以前のバージョンと同じ）
- 複数のキー文字列（それぞれの文字列は引用符に囲まれている必要があります）
- キー文字列のシーケンス

YAML形式は、テキスト形式であるという点では、従来の単一キーのキーファイルと同じです。

新しい形式を使うと、ダウンタイム無しでキーのローリングアップデートが可能となります。[レプリカセットでのキーのローテート](https://docs.mongodb.com/master/tutorial/rotate-key-replica-set/)、[シャードクラスタでのキーのローテート](https://docs.mongodb.com/master/tutorial/rotate-key-sharded-cluster/)を参照してください。

### 一般的なセキュリティに関する改善

- [backup](https://docs.mongodb.com/master/reference/built-in-roles/#backup)組み込みロールに、[serverStatus](https://docs.mongodb.com/master/reference/privilege-actions/#serverStatus)権限が追加されました。

## Aggregationに関する改善

### $outステージの改善

MongoDB 4.2では[$out](https://docs.mongodb.com/master/reference/operator/aggregation/out/#pipe._S_out)ステージに新しいシンタックスが追加されました。新しいシンタックスでは以下のことができます。

- aggregationの出力と既存のコレクションのマージ
- 既存のコレクションの内容をaggregationの出力で置き換え
- 異なるデータベースのコレクションに出力
- シャード化されている既存のコレクションに出力

aggregationの出力と、異なるデータベースの既存のコレクションとのマージを行う例を示します。

```
{ $out: { to : "employees", mode : "insertDocuments", db : "hr" } }
```

従来のシンタックスでは、既存のコレクションの内容をaggregationの出力で置き換えることは出来ましたが、シャード化されたコレクションに出力することはできませんでした。

従来のシンタックスの例を示します。

```
{ $out: "stock" }
```

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/4.2-compatibility/#compat-out">$outステージの制約事項</a></div>

### Aggregationにおける三角関数の追加

MongoDB 4.2では、aggregationパイプライン中で使用可能な三角関数が追加されました。

これらは数値に対して三角関数の計算をします。角度を表す値は、常にラジアンとして解釈されます。度数とラジアンの変換には[$degreesToRadians](https://docs.mongodb.com/master/reference/operator/aggregation/degreesToRadians/#exp._S_degreesToRadians)、[$radiansToDegrees](https://docs.mongodb.com/master/reference/operator/aggregation/radiansToDegrees/#exp._S_radiansToDegrees)を使用してください。

| Name | Description |
|:-----|:------------|
| [$sin](https://docs.mongodb.com/master/reference/operator/aggregation/sin/#exp._S_sin) | サイン |
| [$cos](https://docs.mongodb.com/master/reference/operator/aggregation/cos/#exp._S_cos) | コサイン |
| [$tan](https://docs.mongodb.com/master/reference/operator/aggregation/tan/#exp._S_tan) | タンジェント |
| [$asin](https://docs.mongodb.com/master/reference/operator/aggregation/asin/#exp._S_asin) | アークサイン |
| [$acos](https://docs.mongodb.com/master/reference/operator/aggregation/acos/#exp._S_acos) | アークコサイン |
| [$atan](https://docs.mongodb.com/master/reference/operator/aggregation/atan/#exp._S_atan) | アークタンジェント |
| [$atan2](https://docs.mongodb.com/master/reference/operator/aggregation/atan2/#exp._S_atan2) | y/xのアークタンジェント。yが第1引数、xが第2引数。 |
| [$asinh](https://docs.mongodb.com/master/reference/operator/aggregation/asinh/#exp._S_asinh) | ハイパボリックアークサイン |
| [$acosh](https://docs.mongodb.com/master/reference/operator/aggregation/acosh/#exp._S_acosh) | ハイパボリックアークコサイン |
| [$atanh](https://docs.mongodb.com/master/reference/operator/aggregation/atanh/#exp._S_atanh) | ハイパボリックアークタンジェント |
| [$degreesToRadians](https://docs.mongodb.com/master/reference/operator/aggregation/degreesToRadians/#exp._S_degreesToRadians) | 度数からラジアンに変換 |
| [$radiansToDegrees](https://docs.mongodb.com/master/reference/operator/aggregation/radiansToDegrees/#exp._S_radiansToDegrees) | ラジアンから度数に変換 |

### Aggregationにおける算術式

MongoDB 4.2では[$round](https://docs.mongodb.com/master/reference/operator/aggregation/round/#exp._S_round)が追加されました。数値を特定の桁に丸める場合に[$round](https://docs.mongodb.com/master/reference/operator/aggregation/round/#exp._S_round)を使用してください。

MongoDB 4.2では、[$trunc](https://docs.mongodb.com/master/reference/operator/aggregation/trunc/#exp._S_trunc)に拡張機能と新しいシンタックスが追加されました。新しいシンタックスで[$trunc](https://docs.mongodb.com/master/reference/operator/aggregation/trunc/#exp._S_trunc)を使うことで、数値を特定の桁に切り捨てることができます。

### 新たなステージ

MongoDB 4.2では、新たなaggregationパイプラインステージである[$planCacheStats](https://docs.mongodb.com/master/reference/operator/aggregation/planCacheStats/#pipe._S_planCacheStats)が追加され、これによりあるコレクションに対する[プランキャッシュ](https://docs.mongodb.com/master/core/query-plans/)の情報が取得できるようになりました。

[$planCacheStats](https://docs.mongodb.com/master/reference/operator/aggregation/planCacheStats/#pipe._S_planCacheStats)aggregationステージは、[PlanCache.getPlansByQuery()](https://docs.mongodb.com/master/reference/method/PlanCache.getPlansByQuery/#PlanCache.getPlansByQuery)メソッド、[planCacheListPlans](https://docs.mongodb.com/master/reference/command/planCacheListPlans/#dbcmd.planCacheListPlans)コマンド、[PlanCache.listQueryShapes()](https://docs.mongodb.com/master/reference/method/PlanCache.listQueryShapes/#PlanCache.listQueryShapes)メソッド、[planCacheListQueryShapes](https://docs.mongodb.com/master/reference/command/planCacheListQueryShapes/#dbcmd.planCacheListQueryShapes)コマンドよりも優先されます。

## Change Stream

### startAfterオプション

MongoDB 4.2では、[Change Stream](https://docs.mongodb.com/master/changeStreams/#changestreams)に対してstartAfterオプションが追加されました。レジュームトークンで示されるイベントの後に新しいChange Streamをスタートするようにするものです。このオプションにより、[invalidate](https://docs.mongodb.com/master/reference/change-events/#change-event-invalidate)イベントからChange Streamを開始することができるようになり、以前のStreamが無効化されたあとの通知を見逃さないよう保証することができるようになりました。

### Change Streamレジュームトークン

MongoDB 4.2では、Change Stream[レジュームトークン](https://docs.mongodb.com/master/changeStreams/#change-stream-resume-token)のバージョン1（つまりv1）を使用します。Change Streamレジュームトークン バージョン1は、MongoDB 4.0.7で導入されました。

## Wildcard インデックス

MongoDB 4.2では、ユーザーがコレクション内のさまざまなフィールドに対してクエリを実行するワークロードをサポートするため、ワイルドカードインデックスが追加されました。データベース管理者は、各ユーザーの様々なデータアクセスパターンをサポートするのに多数のインデックスを作成するのではなく、ドキュメントのフィールドの一部、または全てのフィールドを対象としてワイルドカードインデックスを作成できます。

MongoDBは、特定のフィールドパス、または複数のフィールドパスに関連付けられたスカラー値にインデックスをつけます。インデックスキーには、フィールドへのフルパスが含まれます。サブドキュメントを含むフィールドの場合、MongoDBはサブドキュメントの中に降りて、サブドキュメント内のフィールドと値のペアのインデックスキーを作成します。配列であるフィールドの場合は、MongoDBは[マルチキー](https://docs.mongodb.com/master/core/index-multikey/#index-type-multikey)インデックスの場合と同様に、配列内の各値に対してインデックスキーを作成します。

<div><strong>重要：</strong><br />
ワイルドカードインデックスは、ワークロードにもとづいてインデックスを計画することを置き換えられるものではありません。クエリーをサポートするインデックスを作るための詳しい情報は<a href="https://docs.mongodb.com/master/tutorial/create-indexes-to-support-queries/#create-indexes-to-support-queries">こちら</a>を参照してください。</div>

ワイルドカードインデックスは[スパース](https://docs.mongodb.com/master/core/index-sparse/)インデックスの一種で、インデックスには空でない値（スカラー値やnull）を持つフィールドのエントリのみが含まれます。

ワイルドカードインデックスは、[createIndexes](https://docs.mongodb.com/master/reference/command/createIndexes/#dbcmd.createIndexes)コマンド、またはそのシェルヘルパーである[db.collection.createIndex()](https://docs.mongodb.com/master/reference/method/db.collection.createIndex/#db.collection.createIndex)、[db.collection.createIndexes()](https://docs.mongodb.com/master/reference/method/db.collection.createIndexes/#db.collection.createIndexes)を使用して作成することができます。ワイルドカードインデックスの作成例は、[ワイルドカードインデックスを作成する](https://docs.mongodb.com/master/reference/method/db.collection.createIndex/#createindex-method-wildcard-examples)を参照してください。

ワイルドカードインデックスを作成する前に、[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)の[featureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv) (fCV) を4.2に設定する必要があります。fCVの設定方法については、[4.2 機能の互換性](https://docs.mongodb.com/master/release-notes/4.2-compatibility/#compatibility-enabled)を参照してください。

### ワイルドカードインデックスの制約事項

- ワイルドカードインデックスは、以下のインデックス形式、プロパティをサポートしません。
  - [Compound](https://docs.mongodb.com/master/core/index-compound/)
  - [TTL](https://docs.mongodb.com/master/core/index-ttl/)
  - [Text](https://docs.mongodb.com/master/core/index-text/)
  - [2d (Geospatial)](https://docs.mongodb.com/master/core/geospatial-indexes/)
  - [2dsphere (Geospatial)](https://docs.mongodb.com/master/core/2dsphere/)
  - [Hashed](https://docs.mongodb.com/master/core/index-hashed/)
  - [Unique](https://docs.mongodb.com/master/core/index-unique/)
- ワイルドカードインデックスは、以下のクエリオペレータをサポートしません。
  - { $exists: false }
  - { $eq: null }
  - { $eq: [ { a: 1 } ] }
  - { $eq: { \<object\> } } 
  - { $ne: { \<object\> } }
  - { $ne: null } (配列を含むフィールドパスに対するクエリの場合)
- ワイルドカードインデックスを用いてコレクションをシャード化することはできません。
- ワイルドカードインデックスでカバーされている複数のフィールドを指定したクエリに対しては、ワイルドカードインデックスはクエリ内の最大1つのフィールドをサポート可能です。クエリの$or演算子またはaggregationの[$or](https://docs.mongodb.com/master/reference/operator/aggregation/or/#exp._S_or)オペレータを使用しているクエリに対しては、[クエリプランナ](https://docs.mongodb.com/master/core/query-plans/#query-plans-query-optimization)は単一のワイルドカードインデックスを使って、独立した$orの引数を満たすことができます。クエリプランナは、任意のクエリに対してどのワイルドカードインデックスフィールドを使うか選択します。<br />クエリプランナは、**次の場合に限って**、ワイルドカードインデックスを使用して[sort()](https://docs.mongodb.com/master/reference/method/cursor.sort/#cursor.sort)オペレーションもサポートすることができます。
  - ソートフィールドがワイルドカードインデックスに含まれており、_かつ_、
  - ソートフィールドがクエリの条件部分にも含まれており、_かつ_、
  - クエリプランナがクエリを満たすためにソートフィールドを選択した場合

## サポート対象プラットフォーム

- MongoDB 4.2 (Community版およびEnterprise版) は以下のサポートを追加しました。
  - ARM64上でのUbuntu 18.04
- MongoDB 4.2 (Community版およびEnterprise版) は以下のサポートを削除しました。
  - Ubuntu 14.04
  - macOS 10.11

[サポート対象プラットフォーム](https://docs.mongodb.com/master/installation/#mongodb-supported-platforms)も参照してください。

## MongoDB ツール群

### FIPSモード

バージョン4.2以降、--sslFIPSModeオプションは以下のプログラムから削除されました。

- [mongodump](https://docs.mongodb.com/master/reference/program/mongodump/#bin.mongodump)
- [mongoexport](https://docs.mongodb.com/master/reference/program/mongoexport/#bin.mongoexport)
- [mongofiles](https://docs.mongodb.com/master/reference/program/mongofiles/#bin.mongofiles)
- [mongoimport](https://docs.mongodb.com/master/reference/program/mongoimport/#bin.mongoimport)
- [mongorestore](https://docs.mongodb.com/master/reference/program/mongorestore/#bin.mongorestore)
- [mongostat](https://docs.mongodb.com/master/reference/program/mongostat/#bin.mongostat)
- [mongotop](https://docs.mongodb.com/master/reference/program/mongotop/#bin.mongotop)

これらのプログラム群は、接続先の[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)/[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)が[FIPSモードを使うように設定されている](https://docs.mongodb.com/master/tutorial/configure-fips/)場合に限り、FIPSに準拠したコネクションを確立します。

## 一般的な改善

### トラフィックレコーダ

MongoDB 4.2では[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)/[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)に対して送受信される全てのワイヤープロトコルトラフィックをキャプチャーし、指定のファイルに記録するためのトラフィックレコーダが追加されました。

- 有効化するには、[trafficRecordingDirectory](https://docs.mongodb.com/master/reference/parameters/#param.trafficRecordingDirectory)パラメータを起動時に設定する必要があります。
- 開始するには、[startRecordingTraffic](https://docs.mongodb.com/master/reference/command/startRecordingTraffic/#dbcmd.startRecordingTraffic)コマンドを使います。
- 終了するには、[stopRecordingTraffic](https://docs.mongodb.com/master/reference/command/stopRecordingTraffic/#dbcmd.stopRecordingTraffic)コマンドを使います。

[serverStatus](https://docs.mongodb.com/master/reference/command/serverStatus/#dbcmd.serverStatus)コマンドと、[db.serverStatus()](https://docs.mongodb.com/master/reference/method/db.serverStatus/#db.serverStatus)メソッドの出力に、[trafficRecording](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording)の項目が含まれるようになりました。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/4.2/#serverstatus">serverStatusメトリクス</a></div>

### outputConfigオプション

MongoDB 4.2では[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)と[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)に[--outputConfig](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-outputconfig)オプションが追加され、YAML設定ドキュメントを標準出力に出力し、サーバーインスタンスを停止することができるようになりました。

### インデックスキーのサイズ制限の解除

MongoDB 4.2以降で、[featureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv)が4.2またはそれ以上に設定されている場合、[インデックスキーのサイズ制限](https://docs.mongodb.com/master/reference/limits/#Index-Key-Limit)は解除されます。fCVが4.0に設定されている場合は、制限は有効のままです。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/4.2-compatibility/#index-compat-changes">4.2 インデックスに関する互換性の変更点</a></div>

### インデックス名の長さ制限の解除

MongoDB 4.2以降で、[featureCompatibilityVersion](https://docs.mongodb.com/master/reference/command/setFeatureCompatibilityVersion/#view-fcv)が4.2またはそれ以上に設定されている場合、最大127バイトという[インデックス名の長さ制限](https://docs.mongodb.com/master/reference/limits/#Index-Name-Length)は解除されます。以前のバージョンや、fCVが4.0に設定されている場合は、この制限長以内である必要があります。

<div><strong>参照：</strong><br />
<a href="https://docs.mongodb.com/master/release-notes/4.2-compatibility/#index-compat-changes">4.2 インデックスに関する互換性の変更点</a>、<a href="https://docs.mongodb.com/master/release-notes/4.2-compatibility/#compatibility-enabled">4.2 機能の互換性</a></div>

### dropIndexesの改善

#### 複数インデックスのdrop

MongoDB 4.2以降では、[dropIndexes](https://docs.mongodb.com/master/reference/command/dropIndexes/#dbcmd.dropIndexes)コマンドや、その[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルヘルパーである[db.collection.dropIndexes()](https://docs.mongodb.com/master/reference/method/db.collection.dropIndexes/#db.collection.dropIndexes)に対して、複数のインデックスを指定することができるようになりました。複数のインデックスを指定するには、[dropIndexes](https://docs.mongodb.com/master/reference/command/dropIndexes/#dbcmd.dropIndexes)/[db.collection.dropIndexes()](https://docs.mongodb.com/master/reference/method/db.collection.dropIndexes/#db.collection.dropIndexes)に対してインデックス名の配列を渡してください。

#### 関連するクエリのみkill

MongoDB 4.2以降では、dropIndexesと、シェルヘルパーであるdropIndex()、dropIndexes()は、dropされようとしているインデックスを使用しているクエリのみをkillするようになりました。これは、当該インデックスをクエリプランニングの一部として検討中であるクエリも含むかもしれません。

以前のバージョンのMongoDBでは、あるコレクションのインデックスをdropする場合は、そのコレクション上でオープンされているすべてのクエリをkillしていました。

### 設定ファイルにおける外部からの値取り込み

MongoDBは、設定ファイル中で展開ディレクティブを使うことで、外部から値を読み込むことができます。展開ディレクティブは特定の設定ファイルオプションか、もしくは設定ファイル全体を読み込むことができます。

以下の展開ディレクティブが利用可能です。

| 展開ディレクティブ | 説明 |
|:-------------------|:-----|
| [__rest](https://docs.mongodb.com/master/reference/expansion-directives/#configexpansion.__rest) | RESTエンドポイントを指定して、そこから値を読み込みます。 |
| [__exec](https://docs.mongodb.com/master/reference/expansion-directives/#configexpansion.__exec) | シェルやターミナルのコマンドを指定して、そこから値を読み込みます。 |

完全なドキュメントは、[外部からの設定ファイルオプション読み込み](https://docs.mongodb.com/master/reference/expansion-directives/#externally-sourced-values)を参照してください。

### currentOp

MongoDB 4.2では、[$currentOp](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#pipe._S_currentOp) aggregationステージにidleCursorsという新しいオプションが追加され、アイドル状態のカーソルに対する情報が取得できるようになりました。

さらに、MongoDB 4.2では[$currentOp](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#pipe._S_currentOp) aggregationステージ、[currentOp](https://docs.mongodb.com/master/reference/command/currentOp/#dbcmd.currentOp)コマンド、[db.currentOp()](https://docs.mongodb.com/master/reference/method/db.currentOp/#db.currentOp)ヘルパーから返されるドキュメントに、以下の新しいフィールドが追加されました。

| $currentOp | currentOp/db.currentOp() | Description |
|:-----------|:-------------------------|:------------|
| [$currentOp.type](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#_S_currentOp.type) | [currentOp.type](https://docs.mongodb.com/master/reference/command/currentOp/#currentOp.type) | 情報取得対象のオペレーションがop、idleSession、idleCursorのいずれであるかを指定する。 |
| [$currentOp.cursor](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#_S_currentOp.cursor) | [currentOp.cursor](https://docs.mongodb.com/master/reference/command/currentOp/#currentOp.cursor) | カーソルの詳細を指定する。getmoreオペレーションやidleCursorの情報を返す時に利用可能。 |
| [$currentOp.effectiveUsers](https://docs.mongodb.com/master/reference/operator/aggregation/currentOp/#_S_currentOp.effectiveUsers) | [currentOp.effectiveUsers](https://docs.mongodb.com/master/reference/command/currentOp/#currentOp.effectiveUsers) | オペレーションに紐づくユーザーを指定する。 |
| $currentOp.userImpersonators | [currentOp.userImpersonators](https://docs.mongodb.com/master/reference/command/currentOp/#currentOp.userImpersonators) | オペレーションの実効ユーザーになりすますユーザーを指定する。 |

[4.2 current opに関する互換性の変更点](https://docs.mongodb.com/master/release-notes/4.2-compatibility/#current-op-compat)も参照してください。

### ロギング

- [INITSYNC](https://docs.mongodb.com/master/reference/log-messages/#INITSYNC)コンポーネントが追加されました。
- [ELECTION](https://docs.mongodb.com/master/reference/log-messages/#ELECTION)コンポーネントが追加されました。
- デバッグメッセージにはverbosityレベル（D\[1-5\]）が含まれるようになりました。たとえば、verbosityレベルが2の場合は、MongoDBは **D2** というログを出力します。以前のバージョンでは、MongoDBはデバッグレベルでは単にDと出力していました。
- [syslog](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-syslog)にロギングしている場合、メッセージテキストのフォーマットに[コンポーネント名](https://docs.mongodb.com/master/reference/log-messages/#log-message-components)が含まれるようになりました。たとえば以下のようになります。<br />
```
...  ACCESS   [repl writer worker 5] Unsupported modification to roles collection ...
```
以前は、[syslog](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-syslog)でのメッセージテキストにはコンポーネント名は含まれていませんでした。たとえば以下のようになっていました。
```
... [repl writer worker 1] Unsupported modification to roles collection ...
```
- MongoDB 4.2では、[プロファイラログメッセージ](https://docs.mongodb.com/master/tutorial/manage-the-database-profiler/)と[分析ログメッセージ](https://docs.mongodb.com/master/reference/log-messages/)において、[aggregate](https://docs.mongodb.com/master/reference/command/aggregate/#dbcmd.aggregate)オペレーションに関しての **usedDisk** インジケータが追加されました。**usedDisk** は[aggregate](https://docs.mongodb.com/master/reference/command/aggregate/#dbcmd.aggregate)オペレーションのいずれかのステージでメモリの制約により一時ファイルが使用されたことを示します。aggregationでのメモリの制約については[メモリ制約](https://docs.mongodb.com/master/core/aggregation-pipeline-limits/#agg-memory-restrictions)を参照してください。
- バージョン4.2以降（4.0.6以降も含む）では、レプリカセットのセカンダリメンバーは、しきい値以上に長時間かかっているoplog反映処理を、スローログ出力できるようになりました。セカンダリにてREPLコンポーネントとして、 **applied op: \<oplog entry\> took \<num\>ms.** というテキストで[ログ出力](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-logpath)されます。
```
2018-11-16T12:31:35.886-0500 I REPL   [repl writer worker 13] applied op: command { ... }, took 112ms
```
セカンダリにおける、遅いoplog適用のロギングは、
  - [slowOpSampleRate](https://docs.mongodb.com/master/reference/configuration-options/#operationProfiling.slowOpSampleRate)の影響は受けません。つまり、すべての遅いoplog適用がセカンダリでログ出力されます。
  - [logLevel](https://docs.mongodb.com/master/reference/parameters/#param.logLevel)/[systemLog.verbosity](https://docs.mongodb.com/master/reference/configuration-options/#systemLog.verbosity)レベル（または[systemLog.component.replicateion.verbosity](https://docs.mongodb.com/master/reference/configuration-options/#systemLog.component.replication.verbosity)レベル）の影響は受けません。つまり、oplogについては、セカンダリは遅いoplogのみログ出力します。verbosityレベルを上げたとしても、すべてのoplogがログ出力されることはありません。
  - [プロファイラ](https://docs.mongodb.com/master/tutorial/manage-the-database-profiler/)にはキャプチャされません。プロファイリングレベルには影響を受けません。

スローオペレーションのしきい値の設定についてさらなる詳細は以下を参照してください。
  - [mongod --slowms](https://docs.mongodb.com/master/reference/program/mongod/#cmdoption-mongod-slowms)
  - [slowOpThresholdMs](https://docs.mongodb.com/master/reference/configuration-options/#operationProfiling.slowOpThresholdMs)
  - [profile](https://docs.mongodb.com/master/reference/command/profile/#dbcmd.profile)コマンド、またはシェルヘルパーの[db.setProfilingLevel()](https://docs.mongodb.com/master/reference/method/db.setProfilingLevel/#db.setProfilingLevel)メソッド。
- MongoDB 4.2以降では、getLogコマンドは1024文字以上を含むイベントをtruncateします。以前のバージョンでは、getLogは512文字以上の場合truncateしていました。
- MongoDB 4.2以降（および4.0.9）では、[プロファイラエントリ](https://docs.mongodb.com/master/tutorial/manage-the-database-profiler/)や[分析ログメッセージ](https://docs.mongodb.com/master/reference/log-messages/)にスローログとして出力されるオペレーションは、[ストレージ](https://docs.mongodb.com/master/reference/database-profiler/#system.profile.storage)の情報を含むようになりました。
- MongoDB 4.2以降では、read/writeオペレーションに関するプロファイラエントリや分析ログメッセージ（mongod/mongosログメッセージ）は以下の項目を含むようになりました。
  - 同じ[形](https://docs.mongodb.com/master/reference/glossary/#term-query-shape)をしているスロークエリを識別するため **queryHash**
  - スロークエリについて、[クエリプランキャッシュ](https://docs.mongodb.com/master/core/query-plans/)についてさらなる情報を得られるようにするため、**planCacheKey**

[クエリハッシュとプランキャッシュキー](https://docs.mongodb.com/master/release-notes/4.2/#query-plans)を参照してください。

### serverStatusメトリクス

- [shardingStatistics](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.shardingStatistics)に新しい項目が追加されました。
  - [shardingStatistics.countDocsClonedOnRecipient](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.shardingStatistics.countDocsClonedOnRecipient)
  - [shardingStatistics.countDocsClonedOnDonor](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.shardingStatistics.countDocsClonedOnDonor)
  - [shardingStatistics.countDocsDeletedOnDonor](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.shardingStatistics.countDocsDeletedOnDonor)
  - [shardingStatistics.countRecipientMoveChunkStarted](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.shardingStatistics.countRecipientMoveChunkStarted)
- [metrics.repl.network](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.network)に新しい項目が追加されました。
  - [metrics.repl.network.notMasterLegacyUnacknowledgedWrites](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.network.notMasterLegacyUnacknowledgedWrites)
  - [metrics.repl.network.notMasterUnacknowledgedWrites](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.network.notMasterUnacknowledgedWrites)
- プライマリがステップダウンするときに実行中のオペレーションについて情報取得するため、[metrics.repl.stepDown](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.stepDown)という新しい項目が追加されました。[metrics.repl.stepDown](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.stepDown)には以下の項目が含まれます。
  - [metrics.repl.stepDown.userOperationsKilled](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.stepDown.userOperationsKilled)
  - [metrics.repl.stepDown.userOperationRunning](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.metrics.repl.stepDown.userOperationsRunning)
- [トラフィックレコーダ](https://docs.mongodb.com/master/release-notes/4.2/#traffic-recorder)の情報を取得するため、[trafficRecording](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording)という新しい項目が追加されました。[trafficRecording](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording)には以下の項目が含まれます。
  - [trafficRecording.running](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording.running)
  - [trafficRecording.bufferSize](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording.bufferSize)
  - [trafficRecording.bufferedBytes](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording.bufferedBytes)
  - [trafficRecording.recordingFile](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording.recordingFile)
  - [trafficRecording.maxFileSize](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording.maxFileSize)
  - [trafficRecording.currentFileSize](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.trafficRecording.currentFileSize)
- [transactions](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions)に以下の新しい項目が追加されました。
  - [transactions.totalPrepared](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions.totalPrepared)
  - [transactions.totalPreparedThenCommitted](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions.totalPreparedThenCommitted)
  - [transactions.totalPreparedThenAborted](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions.totalPreparedThenAborted)
  - [transactions.currentPrepared](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions.currentPrepared)
  - [transactions.oldestActiveOplogEntryTimestamp](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions.oldestActiveOplogEntryTimestamp)
  - [transactions.oldestOpenUnpreparedReadTimestamp](https://docs.mongodb.com/master/reference/command/serverStatus/#serverstatus.transactions.oldestOpenUnpreparedReadTimestamp)

### zstdの導入

MongoDB 4.2以降では、以下の機能に対して[zstd](https://docs.mongodb.com/master/reference/glossary/#term-zstd)が導入されました。

- ブロック圧縮。[storage.wiredTiger.collectionConfig.blockCompressor](https://docs.mongodb.com/master/reference/configuration-options/#storage.wiredTiger.collectionConfig.blockCompressor)を参照してください。
- ジャーナル圧縮。[storage.wiredTiger.engineConfig.journalCompressor](https://docs.mongodb.com/master/reference/configuration-options/#storage.wiredTiger.engineConfig.journalCompressor)を参照してください。
- ネットワーク圧縮。[net.compression.compressors](https://docs.mongodb.com/master/reference/configuration-options/#net.compression.compressors)を参照してください。
  - MongoDBドライバを使用しているクライアントは、MongoDB 4.2に対応したドライバにアップデートする必要があります。
  - [mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)と[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)のネットワーク圧縮は、デフォルトでは、 **snappy**、**zstd**、**zlib** で、優先順位としてもこの順で適用されます。バージョン4.0では、[mongod](https://docs.mongodb.com/master/reference/program/mongod/#bin.mongod)と[mongos](https://docs.mongodb.com/master/reference/program/mongos/#bin.mongos)のネットワーク圧縮はデフォルトでは **snappy** が利用可能でした。

### queryHashとplanCacheKey

- **queryHash**<br /><div>
同じ[形](https://docs.mongodb.com/master/reference/glossary/#term-query-shape)をしているスロークエリを識別しやすくするため、MongoDB 4.2以降では、クエリの形ごとにqueryHashが割り当てられるようになりました。
</div>
- **planCacheKey**<br /><div>

</div>

- queryHashとplanCacheKeyは以下で利用可能です。
  - profiler entry
  - diagnostic
  - explain()

### $regexと$not

MongoDB 4.2以降（および4.0.7）では、[$not](https://docs.mongodb.com/master/reference/operator/query/not/#op._S_not)オペレータは[$regex](https://docs.mongodb.com/master/reference/operator/query/regex/#op._S_regex)オペレータおよび正規表現オブジェクト（/pattern/形式）の両方に対して、論理否定として働くようになりました。

4.0およびそれ以前のバージョンでは、[$not](https://docs.mongodb.com/master/reference/operator/query/not/#op._S_not)は正規表現オブジェクト（/pattern/形式）に対しては働きましたが、[$regex](https://docs.mongodb.com/master/reference/operator/query/regex/#op._S_regex)オペレータに対しては働きませんでした。

### 自分自身のカーソルをkill

MongoDB 4.2以降では、ユーザーはいつでも、[killCursors](https://docs.mongodb.com/master/reference/privilege-actions/#killCursors)権限を持っているかどうかに関わらず、自分自身のカーソルをkillすることができるようになりました。[killCursors](https://docs.mongodb.com/master/reference/privilege-actions/#killCursors)権限はMongoDB 4.2以降ではこの点については影響を持たなくなりました。

MongoDB 4.0では、自分自身のカーソルをkillするためにも[killCursors](https://docs.mongodb.com/master/reference/privilege-actions/#killCursors)権限が必要でした。

### collStats

MongoDB 4.2以降では、[$collStats](https://docs.mongodb.com/master/reference/operator/aggregation/collStats/#pipe._S_collStats) aggregation、[collStats](https://docs.mongodb.com/master/reference/command/collStats/#dbcmd.collStats)コマンド、[mongo](https://docs.mongodb.com/master/reference/program/mongo/#bin.mongo)シェルヘルパーの[db.collection.stats](https://docs.mongodb.com/master/reference/method/db.collection.stats/#db.collection.stats)は
構築途中のインデックスに関する情報を返すようになりました。

詳細については、以下を参照してください。
- [collStats.nindexes](https://docs.mongodb.com/master/reference/command/collStats/#collStats.nindexes)
- [collStats.indexDetails](https://docs.mongodb.com/master/reference/command/collStats/#collStats.indexDetails)
- [collStats.indexBuilds](https://docs.mongodb.com/master/reference/command/collStats/#collStats.indexBuilds)
- [collStats.totalIndexSize](https://docs.mongodb.com/master/reference/command/collStats/#collStats.totalIndexSize)
- [collStats.indexSizes](https://docs.mongodb.com/master/reference/command/collStats/#collStats.indexSizes)

## 互換性への影響
いくつかの変更点は互換性に影響する可能性があり、ユーザーの対応が必要になるかもしれません。詳細な一覧は[MongoDB 4.2での互換性の変更点](https://docs.mongodb.com/master/release-notes/4.2-compatibility/)を参照してください。

## アップグレードの手順

FEATURE COMPATIBILITY VERSION:<br />
アップグレードするには、4.0のインスタンスはfeatureCompatibilityVersionを4.0にしておく必要があります。

```
db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )
```

featureCompatibilityVersionを確認したり設定したりするための詳細や、アップグレードに関するその他の必要条件、考慮事項などについては、それぞれの手順を参照してください。

- [スタンドアロン環境を4.2にアップグレードする](https://docs.mongodb.com/master/release-notes/4.2-upgrade-standalone/)
- [レプリカセットを4.2にアップグレードする](https://docs.mongodb.com/master/release-notes/4.2-upgrade-replica-set/)
- [シャードクラスタを4.2にアップグレードする](https://docs.mongodb.com/master/release-notes/4.2-upgrade-sharded-cluster/)

4.2へのアップグレードで支援が必要な場合は、[MongoDB社はメジャーバージョンアップグレードサービスを提供しています。](https://www.mongodb.com/products/consulting?jmp=docs)これは、あなたのアプリケーションを停止させることなく円滑な移行ができるように支援するものです。

## 問題を報告する
問題を報告するためには https://github.com/mongodb/mongo/wiki/Submit-Bug-Reports を参照してください。MongoDBサーバや、関連プロジェクトについて、JIRAチケットを登録する方法が書いてあります。
