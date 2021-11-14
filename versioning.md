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

- メジャーリリース（Major Releases）
- ラピッドリリース（Rapid Releases）

MongoDB 4.4およびそれ以前では、プロダクション用バージョンと開発バージョンからなるバージョニングが用いられていました。[過去のリリース](#過去のリリース)を参照してください。

## メジャーリリース

メジャーリリースはおおよそ1年に1回出荷され、たいていは新機能や改善が導入されます。メジャーリリースはすべてのMongoDB利用環境に適切なものです。

*バージョン番号の例：*

- `5.0`
- `6.0`

## ラピッドリリース

Rapid Releases are made available approximately once each quarter
that does not contain a Major Release, and generally mark the
introduction of new features and improvements. Rapid Releases are
designed for use with `MongoDB Atlas
<https://www.mongodb.com/cloud/atlas?tck=docs_server>`_, and are not
generally supported for use in an on-premise capacity. Rapid Releases
are not available for use with `MongoDB Ops Manager
<https://docs.opsmanager.mongodb.com/current/?tck=docs_server>`_, and
are not supported for :ref:`arbiters <replica-set-arbiters>`. If
arbiters are part of your MongoDB deployment, use the most recent
Major Release instead.

*バージョン番号の例：*

- `5.1`
- `5.2`
- `5.3`

## パッチリリース

パッチリリースは、メジャーリリースとラピッドリリースの両方に対して必要に応じてリリースされます。
一般的にはバグ修正や後方互換性を保った変更が含まれます。

*バージョン番号の例：*

- `5.0.1` (a Major Release patch version)
- `5.2.1` (a Rapid Release patch version)

## リリース候補（RC）

In advance of new Major Releases or Rapid Releases, Release Candidates
are made available for early testing. A Release Candidate represents a
version of the upcoming release that is stable enough to begin testing,
but is not suitable for production deployment.

*Example versions:*

- ``5.0.0-rc0``
- ``5.0.0-rc1``
- ``5.1.2-rc5``

## ドライバのバージョン

MongoDBサーバ本体のバージョニングと、[ドライバ](https://docs.mongodb.com/drivers/drivers/)のバージョニングは異なります。

## MongoDB Shell (`mongosh`)

MongoDB 5.0以降、[MongoDB Shell](https://docs.mongodb.com/mongodb-shell/) (`mongosh`) はMongoDBサーバ本体とは個別にリリースされており、バージョニングも異なります。

## データベースツール

MongoDB 4.4以降、[MongoDBデータベースツール](https://docs.mongodb.com/database-tools)はMongoDBサーバ本体とは個別にリリースされており、バージョニングも異なります。

## 過去のリリース

For MongoDB 4.4 and previous, MongoDB versioning used a Production /
Development versioning scheme, and had the form ``X.Y.Z`` where ``X.Y``
refers to either a release series or development series and ``Z`` refers
to the revision/patch number.

- If ``Y`` is even, ``X.Y`` refers to a release series; for example,
  ``4.2`` release series and ``4.4`` release series. Release series are
  **stable** and suitable for production.

- If ``Y`` is odd, ``X.Y`` refers to a development series; for example,
  ``4.3`` development series and ``4.5`` development series.
  Development series are **for testing only and not for production**.

For example, in MongoDB version ``4.4.7``, ``4.4`` refers to the
release series and ``.7`` refers to the revision.
