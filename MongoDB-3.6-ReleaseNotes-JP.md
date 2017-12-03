[Release Notes for MongoDB 3.6](https://docs.mongodb.com/master/release-notes/3.6/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

脚注は、リリースノート以外の3.6のマニュアルも参考にして今回付け加えたものです。

------------------------------

MongoDB 3.6は現在開発中です。

## セキュリティ
### デフォルトでlocalhostにのみバインド
MongoDB 3.6から、MongoDBのバイナリ（mongodおよびmongos）は、デフォルトではlocalhostにのみバインドされるようになりました。以前はMongoDB 2.6以来、公式のMongoDB RPM（Red Hat, CentOS, Fedora Linux、およびその派生）、DEB（Debian, Ubuntu、およびその派生）のみがlocalhostにバインドするようになっていました。詳細は[Localhost Binding Compatibility Changes](https://docs.mongodb.com/master/release-notes/3.6-compatibility/#bind-ip-compatibility)を参照してください。
