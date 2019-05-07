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

脚注は、リリースノート以外の4.2のマニュアルなども参考にして今回付け加えたものです。

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
  - { $eq: { <object> } } 
  - { $ne: { <object> } }
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
