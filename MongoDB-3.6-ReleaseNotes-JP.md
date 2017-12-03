<!-- あとでQiita に投稿する https://qiita.com/kabao/items/ -->
<!-- マニュアルっぽいので「ですます」で -->
<!-- 他のマニュアルを参照する場合、どちらかというと英語タイトルそのままで。文章の一部になっていて、訳さないと変な場合は日本語に訳す。 -->
<!-- NOTE: とかの囲みは、QiitaのMDで対応するものがなさそうなので、注意：brとかしてごまかす-->
[Release Notes for MongoDB 3.6](https://docs.mongodb.com/master/release-notes/3.6/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

脚注は、リリースノート以外の3.6のマニュアルも参考にして今回付け加えたものです。

------------------------------

MongoDB 3.6は現在開発中です。

## セキュリティ
### デフォルトでlocalhostにのみバインド
MongoDB 3.6から、MongoDBのバイナリ（mongodおよびmongos）は、デフォルトではlocalhostにのみバインドされるようになりました。以前はMongoDB 2.6以来、公式のMongoDB RPM（Red Hat, CentOS, Fedora Linux、およびその派生）、DEB（Debian, Ubuntu、およびその派生）のみがlocalhostにバインドするようになっていました。詳細は[Localhost Binding Compatibility Changes](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#bind-ip-compatibility)を参照してください。

### Authentication Restrictions

### Additional Security Enhancements

## Aggregation

### More Expressive ``$lookup``

### New Aggregation Stages

### New Aggregation Operators

### New Aggregation Helper

### New Aggregation Variable

### New Options

### Support for Time Zones

## Array Update Operator Enhancements

### ``arrayFilters``

### Multi-Element Array Updates

### Negative Array Index Position for ``push``

## Change Streams

## Client Sessions

### Causal Consistency

### Retryable Writes

### ``mongo`` Shell Changes

## Server Sessions

### Server Session Commands

### Parameters

### Aggregation Stages

### General

### Command Options

## JSON Schema

## Replica Sets

## Sharded Clusters

## General Enhancements

### MongoDB Compass Packaging

### Collection Identifier

### New Query Operators

### Removed Operators

### Indexes

### Commands

### Wire Protocol and Compression

### Read Concern

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
