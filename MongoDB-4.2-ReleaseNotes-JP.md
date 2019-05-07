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

### passwordPrompt()

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

## Change Stream

## Wildcard インデックス

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
