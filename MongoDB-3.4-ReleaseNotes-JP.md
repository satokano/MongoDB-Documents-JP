<!-- https://qiita.com/kabao/items/19c590d99e724efcf73b -->
MongoDB 3.4 Release Notes 翻訳
[Release Notes for MongoDB 3.4](https://docs.mongodb.com/manual/release-notes/3.4/)の翻訳です。原文はMongoDB Documentation Teamによるものです。ライセンスは[CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/deed.ja)となっています。

[CONTRIBUTING.rst](https://github.com/mongodb/docs/blob/master/CONTRIBUTING.rst)

> MongoDB documentation is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported license.

脚注は、リリースノート以外の3.4のマニュアルも参考にして今回付け加えたものです。

------------------------------

# MongoDB 3.4 リリースノート

MongoDB 3.4が2016年11月29日にリリースされました。
主要な機能としては、linearizable read concern、view、collationが含まれます。

OpsManager 3.4も入手可能です。詳細は[Ops Managerのドキュメント](http://docs.opsmanager.mongodb.com/current/)、および[リリースノート](http://docs.opsmanager.mongodb.com/current/release-notes/application/)を参照してください。

## シャードクラスタ
### Membership Awareness
3.4から、シャードクラスタのコンポーネント（シャード、configサーバ、mongos）はシャードクラスタ内に所属していること、シャードクラスタ名、configサーバの場所を認識するようになりました。

このMembership Awarenessのために、2つの変更がなされています。

- shardsvr指定の必須化<br />
  バージョン3.4のシャードクラスタでは、シャード用の[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)インスタンスは役割をshardsvrとして明確に指定しなければなりません。指定方法は、設定ファイル中の[sharding.clusterRole](https://docs.mongodb.com/manual/reference/configuration-options/#sharding.clusterRole)、コマンドラインオプションの[--shardsvr](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption--shardsvr)のいずれでも構いません。<br />
注意：<br />
shardsvrとして指定された[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)インスタンスのデフォルトポートは27018となります。異なるポートを使うには、[net.port](https://docs.mongodb.com/manual/reference/configuration-options/#net.port)の設定か、--portコマンドラインオプションを指定してください。
- 3.4のmongosは以前のバージョンのmongodと非互換<br />
  バージョン3.4のmongosインスタンスは以前のバージョンのmongodに接続することはできません。

### Balancerプロセスがconfigサーバのプライマリノード上に
balancerプロセスは、従来のmongosノード上から、configサーバレプリカセットのプライマリノード上に移動されました。これに伴い以下の点も変わっています。

- CSRS型のconfigサーバのプライマリノードは"balancer"ロックを保持します。この"balancer"ロックは、"ConfigServer"に指定されたプロセスのプロセスIDを使っており、解放されることはありません。
- MongoDB3.4では以下のコマンド、メソッド群が追加されています。
  - [balancerStart](https://docs.mongodb.com/manual/reference/command/balancerStart/#dbcmd.balancerStart)コマンドおよび、このコマンドをラップした[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellの[sh.startBalancer()](https://docs.mongodb.com/manual/reference/method/sh.startBalancer/#sh.startBalancer)メソッド。3.2およびそれ以前の[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellメソッド[sh.startBalancer()](https://docs.mongodb.com/manual/reference/method/sh.startBalancer/#sh.startBalancer)は、3.4のシャードクラスタとは非互換です。
  - [balancerStop](https://docs.mongodb.com/manual/reference/command/balancerStop/#dbcmd.balancerStop)コマンドおよび、このコマンドをラップした[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellの[sh.stopBalancer()](https://docs.mongodb.com/manual/reference/method/sh.stopBalancer/#sh.stopBalancer)メソッド。3.2およびそれ以前の[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellメソッド[sh.stopBalancer()](https://docs.mongodb.com/manual/reference/method/sh.stopBalancer/#sh.stopBalancer)は、3.4のシャードクラスタとは非互換です。
  - [balancerStatus](https://docs.mongodb.com/manual/reference/command/balancerStatus/#dbcmd.balancerStatus)コマンド。
- 3.4では、[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellの[sh.getBalancerHost()](https://docs.mongodb.com/manual/reference/method/sh.getBalancerHost/#sh.getBalancerHost)メソッドはdeprecated扱いとなりました。3.2およびそれ以前の[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellの[sh.getBalancerHost()](https://docs.mongodb.com/manual/reference/method/sh.getBalancerHost/#sh.getBalancerHost)メソッドは、3.4のシャードクラスタとは非互換です。
- 3.4では、mongosの以下の設定オプションは削除されました。
  - sharding.chunkSize 設定ファイルでの指定、および、コマンドラインでの--chunkSizeの両方。
  - sharding.autoSplit 設定ファイルでの指定、および、コマンドラインでの--noAutoSplitの両方。

### 高速なバランシング
MongoDB 3.4から、以下のように変更されています。

- [WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)では、すべてのチャンクマイグレーションに対して、secondaryThrottleのデフォルト値はfalseです。balancerプロセスはセカンダリへのレプリケーションを待つことはなく、次のドキュメントの処理を継続します。
- MongoDBはチャンクマイグレーションを並行処理できるようになりました。以前のバージョンと似たように、ある1つのシャードはある時点において最大1つまでのマイグレーションに参加可能です。この制約を考えると、n個のシャードからなるシャードクラスタでは、同時に、最大でn/2個（小数点以下切り捨て）のチャンクマイグレーションを実行可能ということになります。

### SCCC型configサーバ構成の非サポート化
3.4のシャードクラスタは、configサーバとしてSCCC型のmongodインスタンスはサポートしません。SCCC型のconfigサーバは3.2リリースでdeprecated扱いとなっていました。代わりに、レプリカセットとしてconfigサーバを構築してください。（CSRS型）

シャードクラスタを3.4にアップグレードするには、configサーバがレプリカセットとして動作している必要があります。

既存のconfigサーバをSCCC型からCSRS型に変換する方法は、[Upgrade Config Servers to Replica Set](https://docs.mongodb.com/manual/tutorial/upgrade-config-servers-to-replica-set/)を参照してください。

### Sharding Zones
MongoDB 3.4では[Zone](https://docs.mongodb.com/manual/core/zone-sharding/)の概念が導入されました。これは以前のバージョンでのtag-awareシャーディングを置き換えるものです。

Zoneをサポートするために、以下のコマンドと[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellのメソッドが追加されました。

| Commands | mongo Shell Methods |
|:-----------|:------------|
| [addShardToZone](https://docs.mongodb.com/manual/reference/command/addShardToZone/#dbcmd.addShardToZone) | [sh.addShardToZone()](https://docs.mongodb.com/manual/reference/method/sh.addShardToZone/#sh.addShardToZone) |
| [removeShardFromZone](https://docs.mongodb.com/manual/reference/command/removeShardFromZone/#dbcmd.removeShardFromZone) | [sh.removeShardFromZone()](https://docs.mongodb.com/manual/reference/method/sh.removeShardFromZone/#sh.removeShardFromZone) |
| [updateZoneKeyRange](https://docs.mongodb.com/manual/reference/command/updateZoneKeyRange/#dbcmd.updateZoneKeyRange) | [sh.updateZoneKeyRange()](https://docs.mongodb.com/manual/reference/method/sh.updateZoneKeyRange/#sh.updateZoneKeyRange)<br />[sh.removeRangeFromZone()](https://docs.mongodb.com/manual/reference/method/sh.removeRangeFromZone/#sh.removeRangeFromZone) |

## レプリカセット
### majority write concernを指定した場合のジャーナリングのデフォルトの挙動
新しい設定項目 [writeConcernMajorityJournalDefault](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault) により、write concernのwオプションに"majority"を指定していて、jオプションは省略している場合の、ジャーナリングのデフォルトの挙動を変えられるようになりました。
writeConcernMajorityJournalDefaultをtrueにすると、これらの書き込みリクエストに対し、過半数のメンバーが書き込みをディスク上のジャーナルに反映した後に応答を返すようになります[^1]。falseに設定すると、過半数のメンバーがメモリまで反映した段階で応答を返します。

### 新しく選出されたプライマリのキャッチアップ期間が調整可能に
新しい設定項目 [settings.catchUpTimeoutMillis](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.settings.catchUpTimeoutMillis) により、新しく選出されたプライマリが、他のレプリカセットメンバーから最新の書き込みを受け取って追いつき反映するまでのタイムアウト時間を設定できるようになりました[^2]。

### Linearizable Read Concern
MongoDB 3.4では["linearizable"](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."linearizable")というread concernレベルが導入されました。"linearizable"は、["majority"](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")を指定されていて、かつ、read処理の開始よりも前に応答が返された、全ての正常なwrite処理を反映した結果を読むためのものです[^3]。linearizable read concernによる保証は、read処理において単一のドキュメントを一意に特定するようなクエリフィルタが指定されている場合のみ受けることができます。

linearizable read concernは、全てのMongoDB ストレージエンジンにおいて利用可能です。

"majority" write concernと"linearizable" read concernを組み合わせると、複数スレッドが単一ドキュメントに対して読み書きをするときに、あたかもシングルスレッドであるかのように動作させることができます。つまり、これらの読み書き処理が実行されるスケジュールは、直線化可能と見なせるということです。

linearizable read concernが指定された読み込みは、majorityやlocalのread concernが指定された場合に比べて、非常に遅くなる可能性があります。linearizable read concernを使う場合は常にmaxTimeMSをともに指定し、過半数のメンバーが障害となっている場合に備えるべきです。例を以下に示します。

```js:
db.restaurants.find( { _id: 5 } ).readConcern("linearizable").maxTimeMS(10000)

db.runCommand( {
     find: "restaurants",
     filter: { _id: 5 },
     readConcern: { level: "linearizable" },
     maxTimeMS: 10000
} )
```

read concernについての詳細、read concernをサポートするコマンド等については、[Read Concern](https://docs.mongodb.com/manual/reference/read-concern/)を参照してください。

### 初期同期の改善

- MongoDB 3.4では、ドキュメントがコピーされたときにインデックスもビルドすることで、[初期同期](https://docs.mongodb.com/manual/core/replica-set-sync/#replica-set-initial-sync)の性能が改善されています。
- MongoDB 3.4では、[初期同期のリトライロジック](https://docs.mongodb.com/manual/core/replica-set-sync/#init-sync-retry)が改善され、一時的なネットワーク障害があった場合でも最終的には同期できるようになりました。
- [rs.status()の出力](https://docs.mongodb.com/manual/reference/command/replSetGetStatus/#rs-status-output)が変更され、初期同期のステータスや進捗状況が表示されるようになりました。

## Decimal型
3.4では、[decimal128フォーマット](https://en.wikipedia.org/wiki/Decimal128_floating-point_format)のdecimalデータ型がサポートされます。decimal128フォーマットは、34桁までの仮数および-6143から+6144までの指数の範囲をサポートします。

このフォーマットをサポートするため、[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellに[NumberDecimal](https://docs.mongodb.com/manual/core/shell-types/#shell-type-decimal)ラッパーが追加されました。

```js:
db.inventory.insert( {_id: 1, item: "The Scream", price: NumberDecimal("9.99"), quantity: 4 } )
```

異なる数値型の間で[比較](https://docs.mongodb.com/manual/reference/bson-type-comparison-order/#bson-types-comparison-order)を行うときには、共通の型に変換することなく、格納されているまさにその値を使って比較します。

近似値を格納しているにすぎないdouble型とは異なり、decimal型は正確な値を格納しています。たとえば、NumberDecimal("9.99")は正確に9.99ですが、double型の9.99は近似値として9.9900000000000002131628...のような値を格納しています。

decimal型を条件として指定するには、[$type](https://docs.mongodb.com/manual/reference/operator/query/type/#op._S_type)演算子と"decimal"、もしくは19[^4]を指定してください。

```js:
db.inventory.find( { price: { $type: "decimal" } } )
```

decimal型をMongoDBのドライバーから使うには、decimal型をサポートしたバージョンのドライバーにアップグレードすることが必要です。

## Aggregation
### 再帰的な検索のためのAggregationステージ
3.4では[Aggregationパイプライン](https://docs.mongodb.com/manual/core/aggregation-pipeline/)に再帰的な検索が可能となるようなステージが導入されました。

| Stage | Description |
|:-----------|:------------|
| [$graphLookup](https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/#pipe._S_graphLookup) | コレクションに対して再帰的に検索を行います。結果として出力される各ドキュメントに対し、そのドキュメントを再帰的に検索したトラバーサル結果の配列フィールドが付加されます。 |

### 複合検索のためのAggregationステージ
複合検索によりドキュメントをカテゴリごとに分類することを可能にします。たとえば、在庫を表すドキュメントのコレクションがあって、値段の範囲などある単一のカテゴリ、または、値段の範囲と部門など複数のカテゴリにしたがって分類したいと思うかも知れません。

3.4では、aggregationパイプラインに複合検索が可能となるようなステージが導入されました。

| Stage | Description |
|:-----------|:------------|
| [$bucket](https://docs.mongodb.com/manual/reference/operator/aggregation/bucket/#pipe._S_bucket) | 入力されたドキュメントを、ある範囲ごとのbucketに分類します。 |
| [$bucketAuto](https://docs.mongodb.com/manual/reference/operator/aggregation/bucketAuto/#pipe._S_bucketAuto) | 入力されたドキュメントを、指定された数のある範囲ごとのbucketに分類します。bucketの境界はMongoDBが自動的に決定します。 |
| [$facet](https://docs.mongodb.com/manual/reference/operator/aggregation/facet/#pipe._S_facet) | 入力されたドキュメントに対して複数の[パイプライン処理](https://docs.mongodb.com/manual/core/aggregation-pipeline/#id1)を行い、これらのパイプラインの結果を含んだドキュメントを出力します。これらのパイプラインに [$bucket](https://docs.mongodb.com/manual/reference/operator/aggregation/bucket/#pipe._S_bucket)、[$bucketAuto](https://docs.mongodb.com/manual/reference/operator/aggregation/bucketAuto/#pipe._S_bucketAuto)、[$sortByCount](https://docs.mongodb.com/manual/reference/operator/aggregation/sortByCount/#pipe._S_sortByCount) など複合検索に関わるステージを指定することで、[$facet](https://docs.mongodb.com/manual/reference/operator/aggregation/facet/#pipe._S_facet) は複合検索を可能にします。 |
| [$sortByCount](https://docs.mongodb.com/manual/reference/operator/aggregation/sortByCount/#pipe._S_sortByCount) | 入力されたドキュメントを、指定の式により数をカウントしてグループに分類します。出力ドキュメントはグループごとの数の降順に並べられます。 |

### ドキュメントの整形を容易にするためのAggregationステージ
3.4ではaggregationパイプラインにドキュメントを置き換えたり、新しいフィールドを追加したりするのを、容易に実現するための新しいステージが導入されました。

| Stage | Description |
|:-----------|:------------|
| [$addFields](https://docs.mongodb.com/manual/reference/operator/aggregation/addFields/#pipe._S_addFields) | ドキュメントにフィールドを追加します。このステージは、入力ドキュメントに含まれていた全てのフィールドと、追加されたフィールドの両方を含んだドキュメントを出力することになります。 |
| [$replaceRoot](https://docs.mongodb.com/manual/reference/operator/aggregation/replaceRoot/#pipe._S_replaceRoot) | 指定された内容でドキュメントを置き換えます。入力ドキュメントの中の埋め込みドキュメントも指定することができ、そうすると埋め込みドキュメントをトップレベルに昇格させることが出来ます。 |

### カウントのためのAggregationステージ
3.4では、ドキュメントをカウントするための新しいステージが[aggregationパイプライン](https://docs.mongodb.com/manual/core/aggregation-pipeline/)に導入されました。

| Operator | Description |
|:-----------|:------------|
| [$count](https://docs.mongodb.com/manual/reference/operator/aggregation/count/#pipe._S_count) | そのステージに入力されたドキュメント数を含むドキュメントを返します。 |

### Aggregationでの配列演算子

| Stage | Description |
|:-----------|:------------|
| [$in](https://docs.mongodb.com/manual/reference/operator/aggregation/in/#exp._S_in) | 指定された値が配列に含まれているかどうか示すbooleanを返す。 |
| [$indexOfArray](https://docs.mongodb.com/manual/reference/operator/aggregation/indexOfArray/#exp._S_indexOfArray) | 配列の中に指定された値が含まれるかどうか検索し、最初に見つかった要素のインデックスを返します。インデックスは0ベースです。 |
| [$range](https://docs.mongodb.com/manual/reference/operator/aggregation/range/#exp._S_range) | 指定範囲の連続した数値からなる配列を返します。 |
| [$reverseArray](https://docs.mongodb.com/manual/reference/operator/aggregation/reverseArray/#exp._S_reverseArray) | 入力された配列を逆順にして返します。 |
| [$reduce](https://docs.mongodb.com/manual/reference/operator/aggregation/reduce/#exp._S_reduce) | 配列を入力として受け取り、各要素に対して処理を行い、結果を返します。 |
| [$zip](https://docs.mongodb.com/manual/reference/operator/aggregation/zip/#exp._S_zip) | 複数の配列を入力として受け取り、各入力配列の1番目の要素を並べた配列を1番目の要素、各入力配列の2番目の要素を並べた配列を2番目の要素・・・とした配列を返します。 |

### AggregationでのString演算子

| Operator | Description |
|:-----------|:------------|
| [$indexOfBytes](https://docs.mongodb.com/manual/reference/operator/aggregation/indexOfBytes/#exp._S_indexOfBytes) | 文字列の中に指定された部分文字列が含まれるかどうか検索し、最初に見つかったUTF-8のバイト単位のインデックスを返します。インデックスは0ベースです。 |
| [$indexOfCP](https://docs.mongodb.com/manual/reference/operator/aggregation/indexOfCP/#exp._S_indexOfCP) | 文字列の中に指定された部分文字列が含まれるかどうか検索し、最初に見つかったUTF-8の[コードポイント](http://www.unicode.org/glossary/#code_point)単位のインデックスを返します。インデックスは0ベースです。 |
| [$split](https://docs.mongodb.com/manual/reference/operator/aggregation/split/#exp._S_split) | 文字列を指定のデリミタで分割し、分割後の文字列の要素を含む配列を返します。 |
| [$strLenBytes](https://docs.mongodb.com/manual/reference/operator/aggregation/strLenBytes/#exp._S_strLenBytes) | 指定された文字列の長さをUTF-8のバイトの長さで返します。 |
| [$strLenCP](https://docs.mongodb.com/manual/reference/operator/aggregation/strLenCP/#exp._S_strLenCP) | 指定された文字列の長さをUTF-8の[コードポイント](http://www.unicode.org/glossary/#code_point)の長さで返します。 |
| [$substrBytes](https://docs.mongodb.com/manual/reference/operator/aggregation/substrBytes/#exp._S_substrBytes) | 文字列の中の部分文字列を返します。返される部分文字列は、第2引数indexで始まり、第3引数countの長さを持ちます。インデックスは0ベースで、UTF-8のバイト単位です。 |
| [$substrCP](https://docs.mongodb.com/manual/reference/operator/aggregation/substrCP/#exp._S_substrCP) | 文字列の中の部分文字列を返します。返される部分文字列は、第2引数indexで始まり、第3引数countの長さを持ちます。インデックスは0ベースで、UTF-8の[コードポイント](http://www.unicode.org/glossary/#code_point)単位です。 |

### Aggregationでのフロー制御式
| Operator | Description |
|:-----------|:------------|
| [$switch](https://docs.mongodb.com/manual/reference/operator/aggregation/switch/#exp._S_switch) | 指定されたcase式を順に評価し、はじめにtrueとなったブランチにしたがって処理を継続します。 |

### 日付処理に関するAggregation演算子
| Operator | Description |
|:-----------|:------------|
| [$isoDayOfWeek](https://docs.mongodb.com/manual/reference/operator/aggregation/isoDayOfWeek/#exp._S_isoDayOfWeek) | ISO 8601にもとづいて週の日を表現する数値を返します。月曜日が1、日曜日が7となります。 |
| [$isoWeek](https://docs.mongodb.com/manual/reference/operator/aggregation/isoWeek/#exp._S_isoWeek) | ISO 8601にもとづいて週を表現する1から53までの数値を返します。1年の最初の月曜日から日曜日までそろった週が、番号1となります。 |
| [$isoWeekYear](https://docs.mongodb.com/manual/reference/operator/aggregation/isoWeekYear/#exp._S_isoWeekYear) | ISO 8601にもとづいて年の週を表現する数値を返します。1年は、番号1の週の月曜日から始まり、最終週の日曜日で終わることになります。 |

### Aggregation元に対する統計監視
| Operator | Description |
|:-----------|:------------|
| [$collStats](https://docs.mongodb.com/manual/reference/operator/aggregation/collStats/#pipe._S_collStats) | コレクションまたはビューに関する統計情報を返します。 |

### 型判別演算子
| Operator | Description |
|:-----------|:------------|
| [$type](https://docs.mongodb.com/manual/reference/operator/aggregation/type/#exp._S_type) | 引数のBSON型を表す文字列を返します。 |

### 追加的な変更点
[$project](https://docs.mongodb.com/manual/reference/operator/aggregation/project/#pipe._S_project) ステージは、出力ドキュメントで特定のフィールドの除外できるようになりました。以前は、_idフィールドのみ除外可能でした。フィールドの除外を指定するときは、

- 出力ドキュメントには、それ以外の全てのフィールドが含まれた形で返却されます。
- 新しいフィールドを指定したり、他のフィールドを含めるように指定することはできません。

## Collation（照合順序）およびCase-Insensitiveインデックス
言語に特有のルールにもとづいて文字列の比較が行えるようにするため、MongoDB 3.4ではクエリおよびインデックスに対して[collation（照合順序）](https://docs.mongodb.com/manual/reference/collation/)が導入されました。

以下のコマンドとメソッドがcollation（照合順序）をサポートしています。

| Commands | mongo Shell Methods |
|:-----------|:------------|
| [create](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | [db.createCollection()](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)<br />[db.createView()](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView) |
| [createIndexes](https://docs.mongodb.com/manual/reference/command/createIndexes/#dbcmd.createIndexes) | [db.collection.createIndex()](https://docs.mongodb.com/manual/reference/method/db.collection.createIndex/#db.collection.createIndex) |
| [create](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | [db.createCollection()](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)<br />[db.createView()](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView) |
| [aggregate](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) | [db.collection.aggregate()](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate) |
| [distinct](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct) | [db.collection.distinct()](https://docs.mongodb.com/manual/reference/method/db.collection.distinct/#db.collection.distinct) |
| [findAndModify](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) | [db.collection.findAndModify()](https://docs.mongodb.com/manual/reference/method/db.collection.findAndModify/#db.collection.findAndModify)<br />[db.collection.findOneAndDelete()](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndDelete/#db.collection.findOneAndDelete)<br />[db.collection.findOneAndReplace()](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndReplace/#db.collection.findOneAndReplace)<br />[db.collection.findOneAndUpdate()](https://docs.mongodb.com/manual/reference/method/db.collection.findOneAndUpdate/#db.collection.findOneAndUpdate) |
| [find](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find) | [db.collection.find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)でのcollationを設定するためには[cursor.collation()](https://docs.mongodb.com/manual/reference/method/cursor.collation/#cursor.collation)を使います。 |
| [mapReduce](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) | [db.collection.mapReduce()](https://docs.mongodb.com/manual/reference/method/db.collection.mapReduce/#db.collection.mapReduce) |
| [delete](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete) | [db.collection.deleteOne()](https://docs.mongodb.com/manual/reference/method/db.collection.deleteOne/#db.collection.deleteOne)<br />[db.collection.deleteMany()](https://docs.mongodb.com/manual/reference/method/db.collection.deleteMany/#db.collection.deleteMany)<br />[db.collection.remove()](https://docs.mongodb.com/manual/reference/method/db.collection.remove/#db.collection.remove) |
| [update](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) | [db.collection.update()](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update)<br />[db.collection.updateOne()](https://docs.mongodb.com/manual/reference/method/db.collection.updateOne/#db.collection.updateOne)<br />[db.collection.updateMany()](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/#db.collection.updateMany)<br />[db.collection.replaceOne()](https://docs.mongodb.com/manual/reference/method/db.collection.replaceOne/#db.collection.replaceOne) |
| [shardCollection](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection) | [sh.shardCollection()](https://docs.mongodb.com/manual/reference/method/sh.shardCollection/#sh.shardCollection) |
| | [db.collection.bulkWrite()](https://docs.mongodb.com/manual/reference/method/db.collection.bulkWrite/#db.collection.bulkWrite)の中の個別のupdate、replace、deleteに対しても指定可能です。 |

詳細は、[Collation](https://docs.mongodb.com/manual/reference/collation/)を参照してください。

## ビュー
MongoDB 3.4では、既存のコレクション（または他のビュー）をもとに、ビューを作成することができるようになりました。ビューは読み込み専用となります。ビューを定義するためにMongoDB 3.4では以下の機能が導入されています。

- 既存の[create](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create)コマンドに対する、viewOnおよびpipelineオプションの追加

```js:
db.runCommand( { create: <view>, viewOn: <source>, pipeline: <pipeline> } )
```

ビューに対してデフォルトのcollation（照合順序）も指定する場合は

```js:
db.runCommand( { create: <view>, viewOn: <source>, pipeline: <pipeline>, collation: <collation> } )
```

- そしてそれに紐付く、[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shellの[db.createView()メソッド](https://docs.mongodb.com/manual/reference/method/db.createView/#db.createView)

```js:
db.createView(<view>, <source>, <pipeline>, <collation>)
```

ビューの作成に関する詳細は[Read-only Views](https://docs.mongodb.com/manual/core/views/#reference-views)を参照してください。

## セキュリティの強化
### クラスタ内部認証を有効化する場合の移行支援機能
MongoDB 3.4では、レプリカセットやシャードクラスタにおいて、無停止でクラスタ内部認証を有効化するための移行支援機能が追加されました。詳細については、[security.transitionToAuth](https://docs.mongodb.com/manual/reference/configuration-options/#security.transitionToAuth)の設定、または[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)やmongosの[--transitionToAuth](https://docs.mongodb.com/manual/reference/program/mongos/#cmdoption--transitionToAuth)コマンドラインオプションの説明を参照してください。

参照：
[Enforce Keyfile Access Control in a Replica Set without Downtime](https://docs.mongodb.com/manual/tutorial/enforce-keyfile-access-control-in-existing-replica-set-without-downtime/)

## MongoDBツール群
### mongoreplay
mongoreplayというツールが導入されました。mongoreplayは、作業手順をキャプチャして解析するツールで、mongosniffを置き換えるものです。mongoreplayを使うことで、MongoDBインスタンスに送られたコマンドを調査したり記録したりすることができ、さらに、後で他のホストでコマンドをリプレイすることもできます。

## 一般的な改善
MongoDB 3.4では以下のような改善が含まれています。

- systemdのサポートが追加されました。
- [diagnosticDataCollectionDirectorySizeMB](https://docs.mongodb.com/manual/reference/parameters/#param.diagnosticDataCollectionDirectorySizeMB)のデフォルト値が100MBから200MBに増やされました。
- [WiredTigerのキャッシュサイズ](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption--wiredTigerCacheSizeGB)の下限およびデフォルト値が下げられました。[WiredTigerのキャッシュサイズ](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption--wiredTigerCacheSizeGB)、および、[インメモリストレージエンジンの最大メモリサイズ](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption--inMemorySizeGB)に、浮動小数を指定できるようになりました。
- [find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)、[aggregate()](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)、[listIndexes](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes)、[listCollections](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections)が返す[batchあたりの最大サイズは16MB](https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/#cursor-batches)になりました。
- [db.currentOp()](https://docs.mongodb.com/manual/reference/method/db.currentOp/#db.currentOp)と[データベースプロファイラ](https://docs.mongodb.com/manual/reference/database-profiler/)は、すべてのCRUDオペレーションに対して、下記の情報を含む、同様の基礎的な診断情報を報告します。<br />

 - [aggregate](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate)
 - [count](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count)
 - [delete](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete)
 - [distinct](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct)
 - find ([OP_QUERY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-query) および [command](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find))
 - [findAndModify](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify)
 - [geoNear](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear)
 - getMore ([OP_GET_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-query) および [command](https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore))
 - [group](https://docs.mongodb.com/manual/reference/command/group/#dbcmd.group)
 - [insert](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert)
 - [mapReduce](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce)
 - [update](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update)

これらのオペレーションはスロークエリログにも含まれます。スロークエリログの詳細は[slowOpThresholdMs](https://docs.mongodb.com/manual/reference/configuration-options/#operationProfiling.slowOpThresholdMs)を参照してください。

- BSONの[javascript型](https://docs.mongodb.com/manual/reference/bson-types/#bson-types)および[javascriptwithscope型](https://docs.mongodb.com/manual/reference/bson-types/#bson-types)の要素を、JavaScriptのファンクションとして実行する機能の有効/無効を切り替える機能が[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)シェルに追加されました。[--disableJavaScriptProtection](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption--disableJavaScriptProtection)を参照してください。
- システム証明書のサポートが追加されました。[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)インスタンスが、オペレーティングシステムによって信頼済みのCAによって署名された証明書を提示した場合、[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)シェルはエラー無しで接続します。以前は、[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)シェルは証明書を検証できないというエラーを出力してexitしていました。

## プラットフォーム サポート

- MongoDB 3.4では、ARM64、PPC64LE、s390xアーキテクチャーがサポート対象に追加されました。完全なプラットフォーム サポートの一覧は[Supported Platforms](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-supported-platforms)を参照してください。
- 警告：<br />
  3.4のIBM Power Systems上のUbuntu 16.04 での非互換<br />
  [glibcでのロックの脱落のバグ](https://bugs.launchpad.net/ubuntu/+source/glibc/+bug/1640518)のため、IBM Power Systems上のUbuntu 16.04を使っている場合、glibcの修正バージョンが出るまでは、MongoDB 3.4をプロダクションで使うべきではありません。

## MongoDB Enterprise版の機能
### ログの編集（マスキング）
MongoDB 3.4 Enterpriseでは、ログの編集機能が追加されました。これは[暗号化ストレージエンジン](https://docs.mongodb.com/manual/core/security-encryption-at-rest/)とともに使うためのものです。ログの編集機能は潜在的に機密性の高い情報が診断ログに出力されてしまうのを防ぐものです。ただし、編集済みのログを使った診断は、イベントに関する情報が欠落している可能性があるため、困難になる可能性があります。

ログの編集機能を有効にするには、[security.redactClientLogData](https://docs.mongodb.com/manual/reference/configuration-options/#security.redactClientLogData)の設定、および、[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)の[--redactClientLogData](https://docs.mongodb.com/manual/reference/program/mongos/#cmdoption--redactClientLogData)オプションを参照してください。

### LDAPの改善
#### LDAPによる認可
MongoDB 3.4 Enterpriseでは、以下のいずれかの機構により認証されたユーザに対して、Lightweight Directory Access Protocol （LDAP） を使って認可（つまりアクセス許可）を行うことができるようになりました。

- [LDAP Proxy認証](https://docs.mongodb.com/manual/core/security-ldap/)。LDAP認証と認可の両方を使うチュートリアルは、[Authenticate and Authorize Users Using Active Directory via Native LDAP](https://docs.mongodb.com/manual/tutorial/authenticate-nativeldap-activedirectory/)を参照してください。
- [Kerberos認証](https://docs.mongodb.com/manual/core/kerberos/)。Kerberos認証とActive Directoryを使うチュートリアルは、[Configure MongoDB with Kerberos Authentication and Active Directory Authorization](https://docs.mongodb.com/manual/tutorial/kerberos-auth-activedirectory-authz/)を参照してください。
- [x.509](https://docs.mongodb.com/manual/core/security-x.509/)

詳細は、[LDAP Authorization](https://docs.mongodb.com/manual/core/security-ldap-external/)を参照してください。

#### mongoldap
MongoDB 3.4 Enterpriseでは、[mongoldap](https://docs.mongodb.com/manual/reference/program/mongoldap/#bin.mongoldap)という新しいツールが提供されます。これはMongoDBの[LDAP関連の設定](https://docs.mongodb.com/manual/reference/configuration-options/#security-ldap-options)を、稼働しているLDAPサーバ群に対してテストするためのものです。[LDAP認証](https://docs.mongodb.com/manual/core/security-ldap/#security-ldap)に関する設定をするときには、[mongoldap](https://docs.mongodb.com/manual/reference/program/mongoldap/#bin.mongoldap)を使って、意図通りに設定できているか確認することができます。

#### OSのライブラリを使ったバインド
MongoDB 3.4 Enterpriseでは、オペレーティングシステムのライブラリを使った[LDAPサーバーとのバインド](https://docs.mongodb.com/manual/core/security-ldap/)がサポートされました。これによりLinuxおよびWindowsにおいてMongoDB 3.4はLDAPサーバーを認証のために使うことができるようになりました。

Linux環境では、引き続き[saslauthd](http://www.linuxcommand.org/man_pages/saslauthd8.html)を使ったバインドもサポートされます。

## 互換性への影響
いくつかの変更点は互換性に影響する可能性があり、ユーザーの対応が必要になるかもしれません。詳細な一覧は[MongoDB 3.4での互換性の変更点](https://docs.mongodb.com/manual/release-notes/3.4-compatibility/)を参照してください。

## アップグレードの手順
* [スタンドアロン構成のサーバを3.4にアップグレードする](https://docs.mongodb.com/manual/release-notes/3.4-upgrade-standalone/)
* [レプリカセットを3.4にアップグレードする](https://docs.mongodb.com/manual/release-notes/3.4-upgrade-replica-set/)
* [シャードクラスタを3.4にアップグレードする](https://docs.mongodb.com/manual/release-notes/3.4-upgrade-sharded-cluster/)

3.4へのアップグレードで支援が必要な場合は、[MongoDB社はメジャーバージョンアップグレードサービスを提供しています。](https://www.mongodb.com/products/consulting)これは、あなたのアプリケーションを停止させることなく円滑な移行ができるように支援するものです。

## 3.4.0での既知のバグ
3.4.0での既知のバグは以下のものがあります。

* [SERVER-27124](https://jira.mongodb.org/browse/SERVER-27124) : protocolVersion: 0 が"majority" read concernを適切にサポートできていない。
* [SERVER-27195](https://jira.mongodb.org/browse/SERVER-27195) : BSONのSymbol型を含んでいるドキュメントでのcollationの使用はサポートされておらず、未定義の動作となる。Symbol型はdeprecated。
* [SERVER-27207](https://jira.mongodb.org/browse/SERVER-27207) : mongos経由のアクセスで、viewに対して、sort付きでfindすると、不正に空の結果が返却されることがある。

------------------------------

[^1]: writeConcernMajorityJournalDefaultをtrueにするときは、レプリカセット中の全メンバーでジャーナリングを有効にする必要があります。[writeConcernMajorityJournalDefault](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.writeConcernMajorityJournalDefault)

[^2]: キャッチアップ期間中は、新プライマリは書き込みを受け付けません。この期間中に新プライマリに反映できなかった書き込みは、他メンバー（セカンダリ）側でロールバックとなります。[settings.catchUpTimeoutMillis](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.settings.catchUpTimeoutMillis)

[^3]: writeConcernMajorityJournalDefaultをtrueにしている環境では、linearizable read concern指定の読み込みは、ロールバックされないデータを返却することが保証されます。一方、writeConcernMajorityJournalDefaultをfalseにしている場合は、wオプションにmajorityを指定した書き込みであってもMongoDBはそれが永続化されるのを待たずに応答を返すため、障害発生時にはロールバックされる可能性があります。また、linearizable read concernを指定できるのは、プライマリに対する読み込みのみです。[linearizable](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."linearizable")

[^4]: 19はdecimal型を意味する定数。[Available Types](https://docs.mongodb.com/manual/reference/operator/query/type/#document-type-available-types)