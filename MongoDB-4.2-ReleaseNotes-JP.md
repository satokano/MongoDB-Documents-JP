<!-- Qiita  -->
<!-- マニュアルっぽいので「ですます」で -->
<!-- 他のマニュアルを参照する場合、どちらかというと英語タイトルそのままで。文章の一部になっていて、訳さないと変な場合は日本語に訳す。 -->
<!-- NOTE: とかの囲みは、QiitaのMDで対応するものがなさそうなので、注意：brなどとしてごまかす？ ```text: かと思ったがそうすると中身のMD記法が解釈されない。divかと思ったがQiitaはstyle属性適用されない。divで囲んでタイトルはstrong、中身は自前でタグを書くのが見た目的には一番マシっぽい。 -->
<!-- SEE ALSO: は参照：、WARNING: は警告： -->
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
MongoDB 4.2では、すでにdeprecated扱いであったMMAPv1ストレージエンジンが廃止になりました。

現在4.0の環境でMMAPv1を使用している場合は、MongoDB 4.2にアップグレードする前に、[WiredTigerストレージエンジン](https://docs.mongodb.com/master/core/wiredtiger/)に変更する必要があります。詳細については以下を参照してください。

- [スタンドアロン環境をWiredTigerに変更する](https://docs.mongodb.com/master/tutorial/change-standalone-wiredtiger/)
- [レプリカセットをWiredTigerに変更する](https://docs.mongodb.com/master/tutorial/change-replica-set-wiredtiger/)
- [シャードクラスタをWiredTigerに変更する](https://docs.mongodb.com/master/tutorial/change-sharded-cluster-wiredtiger/)

### MMAPv1特有の設定オプション

### MMAPv1特有のパラメータ

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

## シャードクラスタ

## セキュリティに関する改善

## Aggregationに関する改善

## Change Stream

## Wildcard インデックス

## サポート対象プラットフォーム

## MongoDB ツール群

## 一般的な改善

## 互換性への影響
いくつかの変更点は互換性に影響する可能性があり、ユーザーの対応が必要になるかもしれません。詳細な一覧は[MongoDB 4.2での互換性の変更点](https://docs.mongodb.com/master/release-notes/4.2-compatibility/)を参照してください。

## アップグレードの手順

FEATURE COMPATIBILITY VERSION:<br />
アップグレードするには、4.0のインスタンスはfeatureCompatibilityVersionを4.0にしておく必要があります。

```
db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )
```

## 問題を報告する
問題を報告するためには https://github.com/mongodb/mongo/wiki/Submit-Bug-Reports を参照してください。MongoDBサーバや、関連プロジェクトについて、JIRAチケットを登録する方法が書いてあります。

------------------------------
