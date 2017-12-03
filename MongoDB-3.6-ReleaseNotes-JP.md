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

### Additional Enhancements

## 互換性への影響
いくつかの変更点は互換性に影響する可能性があり、ユーザーの対応が必要になるかもしれません。詳細な一覧は[MongoDB 3.6での互換性の変更点](https://docs.mongodb.com/master/release-notes/3.6-compatibility/)を参照してください。

## Upgrade Procedures

## Download

## Report an Issue



