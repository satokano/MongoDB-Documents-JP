<!-- Qiita https://qiita.com/kabao/items/9e7726624708d0ed6caf -->
<!-- マニュアルっぽいので「ですます」で -->
<!-- 他のマニュアルを参照する場合、どちらかというと英語タイトルそのままで。文章の一部になっていて、訳さないと変な場合は日本語に訳す。 -->
<!-- NOTE: とかの囲みは、QiitaのMDで対応するものがなさそうなので、注意：brなどとしてごまかす？ ```text: かと思ったがそうすると中身のMD記法が解釈されない。divかと思ったがQiitaはstyle属性適用されない。divで囲んでタイトルはstrong、中身は自前でタグを書くのが見た目的には一番マシっぽい。 -->
<!-- SEE ALSO: は参照：、WARNING: は警告： -->
<!-- deprecated 非推奨 -->
<!-- 英単語・数字の周りに空白をいれるか？ -->
<!-- restの`、``と、Qiita Markdownの対応は？ -->
[MongoDB Versioning](https://docs.mongodb.com/manual/reference/versioning/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

------------------------------

# MongoDB バージョニング

<div><strong>重要：</strong><br />
常に当該リリースの最新安定リビジョンにアップグレードするようにしてください。</div>

MongoDBのバージョン番号は`X.Y.Z`という形を持っており、`X.Y`はリリースを表し、`Z`はパッチ番号を表します。

MongoDB 5.0以降、MongoDBは2つの異なるリリースシリーズでリリースされます。

- メジャーリリース
- ラピッドリリース

MongoDB 4.4およびそれ以前では、プロダクション用バージョンと開発バージョンからなるバージョニングが用いられていました。[過去のリリース](#過去のリリース)を参照してください。

## メジャーリリース

メジャーリリースはおおよそ1年に1回出荷され、たいていは新機能や改善が導入されます。メジャーリリースはすべてのMongoDB利用環境に適切なものです。

*バージョン番号の例：*

- `5.0`
- `6.0`

## ラピッドリリース

ラピッドリリースは、おおよそ、メジャーリリースの無い四半期ごとに出荷され、たいていは新機能や改善が導入されます。
ラピッドリリースは[MongoDB Atlas](https://www.mongodb.com/cloud/atlas?tck=docs_server)で使われるよう設計されており、オンプレミス環境では一般的にはサポートされません。
ラピッドリリースは[MongoDB Ops Manager](https://docs.opsmanager.mongodb.com/current/?tck=docs_server)とともに利用することはできず、[アービター](https://docs.mongodb.com/manual/core/replica-set-members/#std-label-replica-set-arbiters)はサポートされません。
もしMongoDB環境の一部にアービターを利用している場合は、最新のメジャーリリースを利用してください。

*バージョン番号の例：*

- `5.1`
- `5.2`
- `5.3`

## パッチリリース

パッチリリースは、メジャーリリースとラピッドリリースの両方に対して必要に応じてリリースされます。
一般的にはバグ修正や後方互換性を保った変更が含まれます。

*バージョン番号の例：*

- `5.0.1` (メジャーリリースとパッチバージョン)
- `5.2.1` (ラピッドリリースとパッチバージョン)

## リリース候補（RC）

メジャーリリースやラピッドリリースの前に、早期のテストのためリリース候補が出荷されます。
リリース候補は次のリリースが十分に安定しているかどうかテストするために出荷されますが、プロダクション環境には適切ではありません。

*バージョン番号の例：*

- `5.0.0-rc0`
- `5.0.0-rc1`
- `5.1.2-rc5`

## ドライバのバージョン

MongoDBサーバ本体のバージョニングと、[ドライバ](https://docs.mongodb.com/drivers/drivers/)のバージョニングは異なります。

## MongoDB Shell (`mongosh`)

MongoDB 5.0以降、[MongoDB Shell](https://docs.mongodb.com/mongodb-shell/) (`mongosh`) はMongoDBサーバ本体とは個別にリリースされており、バージョニングも異なります。

## データベースツール

MongoDB 4.4以降、[MongoDBデータベースツール](https://docs.mongodb.com/database-tools)はMongoDBサーバ本体とは個別にリリースされており、バージョニングも異なります。

## 過去のリリース

MongoDB 4.4およびそれ以前では、プロダクション用バージョンと開発バージョンに分ける考え方が採用されており、
バージョン番号は`X.Y.Z`の形をしていて、`X.Y`はプロダクション用バージョンもしくは開発バージョンを示し、`Z`はリビジョン/パッチ番号を示していました。

- `Y`が偶数の場合、`X.Y`はプロダクション用バージョンを示します。たとえば`4.2`系や`4.4`系がそれにあたります。これらのプロダクション用リリースは**安定**していてプロダクション環境に適しています。

- `Y`が奇数の場合、`X.Y`は開発バージョンを示します。たとえば`4.3`系や`4.5`系がそれにあたります。開発用リリースは**テスト目的限定でありプロダクション環境には適しません**。

たとえばMongoDB `4.4.7`では、`4.4`はプロダクション用の系列を示し、`.7`はリビジョンを示します。
