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

<!-- 脚注は、リリースノート以外の4.2のマニュアルなども参考にして今回付け加えたものです。 -->

------------------------------

# MongoDB バージョニング

<div><strong>重要：</strong><br />
常に当該リリースの最新安定リビジョンにアップグレードするようにしてください。
</div>

MongoDBのバージョン番号は`X.Y.Z`という形を持っており、`X.Y`はリリースを表し、`Z`はパッチ番号を表します。

MongoDB 5.0以降、MongoDBは2つの異なるリリースシリーズでリリースされます。

- メジャーリリース（Major Releases）
- ラピッドリリース（Rapid Releases）

MongoDB 4.4およびそれ以前では、プロダクション用バージョンと開発バージョンからなるバージョニングが用いられていました。[過去のリリース](#過去のリリース)を参照してください。

Major Releases
--------------

Major Releases are made available approximately once a year, and
generally mark the introduction of new features and improvements.
Major Releases are appropriate for all MongoDB deployments.

*Example versions:*

- ``5.0``
- ``6.0``

Rapid Releases
--------------

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

*Example versions:*

- ``5.1``
- ``5.2``
- ``5.3``

Patch Releases
--------------

Patch Releases are made available as needed to both
Major Releases and Rapid Releases, and generally
include bug fixes and backwards-compatible changes.

*Example versions:*

- ``5.0.1`` (a Major Release patch version)
- ``5.2.1`` (a Rapid Release patch version)

Release Candidate (RC) Releases
-------------------------------

In advance of new Major Releases or Rapid Releases, Release Candidates
are made available for early testing. A Release Candidate represents a
version of the upcoming release that is stable enough to begin testing,
but is not suitable for production deployment.

*Example versions:*

- ``5.0.0-rc0``
- ``5.0.0-rc1``
- ``5.1.2-rc5``

Driver Versions
---------------

The version numbering system for MongoDB differs from the system
used for the :ecosystem:`MongoDB drivers </drivers>`.

MongoDB Shell (``mongosh``)
---------------------------

Starting with MongoDB 5.0, the `MongoDB Shell
<https://docs.mongodb.com/mongodb-shell/>`__ (``mongosh``) is released
separately from the MongoDB Server, and uses its own version numbering
system.

Database Tools
--------------

Starting with MongoDB 4.4, the `MongoDB Database Tools
<https://docs.mongodb.com/database-tools>`__ are released separately
from the MongoDB Server, and use their own version numbering system.

.. _historical-releases:

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
