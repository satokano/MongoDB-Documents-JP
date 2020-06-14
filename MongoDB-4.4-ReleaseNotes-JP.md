Release Notes for MongoDB 4.4 (Release Candidate)
=================================================

::: {.default-domain}
mongodb
:::

::: {.contents}
On this page
:::

Aggregation {#4.4-rel-notes-agg}
-----------

### Union All (`$unionWith` Stage)

MongoDB 4.4 adds the [\$unionWith]{role="pipeline"} aggregation stage,
providing the ability to combines pipeline results from multiple
collections into a single result set.

For details, see [\$unionWith]{role="pipeline"}.

### Custom Aggregation Expressions

Starting in version 4.4, MongoDB provides the following new operators
that allow users to define custom aggregation expressions:

-   [\$accumulator]{role="group"}
-   [\$function]{role="expression"}

With the addition of these new operators, you can use aggregation to
write custom JavaScript expressions instead of relying on
[mapReduce]{role="dbcommand"} and [\$where]{role="query"}.

::: {.note}
::: {.admonition-title}
Note
:::

Even before version 4.4, various map-reduce expressions could also be
rewritten using [other aggregation pipeline operators
\</meta/aggregation-quick-reference\>]{role="doc"}, such as
[\$group]{role="pipeline"}, [\$merge]{role="pipeline"}, etc., without
requiring custom functions.
:::

For more information, see
[/reference/map-reduce-to-aggregation-pipeline]{role="doc"}.

### New Aggregation Operators {#4.4-rel-notes-new-agg-operators}

+-------------+--------------------------------------------------------+
| Operator    | Description                                            |
+=============+========================================================+
| [\$accumula | Returns the result of a user-defined                   |
| tor]{role=" | [accumulator operator \<agg-operators-group-accumulato |
| group"}     | rs\>]{role="ref"}.                                     |
+-------------+--------------------------------------------------------+
| [\$binarySi | Returns the size of a given string or binary data      |
| ze]{role="e | value\'s content in bytes.                             |
| xpression"} |                                                        |
+-------------+--------------------------------------------------------+
| [\$bsonSize | Returns the size in bytes of a given document (i.e.    |
| ]{role="exp | bsontype `Object`) when encoded as                     |
| ression"}   | [BSON]{role="term"}.                                   |
+-------------+--------------------------------------------------------+
| [\$first]{r | Returns the first element in an array.                 |
| ole="expres |                                                        |
| sion"}      |                                                        |
+-------------+--------------------------------------------------------+
| [\$function | Defines a custom aggregation expression.               |
| ]{role="exp |                                                        |
| ression"}   |                                                        |
+-------------+--------------------------------------------------------+
| [\$last]{ro | Returns the last element in an array.                  |
| le="express |                                                        |
| ion"}       |                                                        |
+-------------+--------------------------------------------------------+
| [\$isNumber | Returns boolean `true` if the specified expression     |
| ]{role="exp | resolves to an [integer \<Int32\>]{role="bsontype"},   |
| ression"}   | [decimal                                               |
|             | \<Decimal128\>]{role="bsontype"},                      |
|             | [double \<Double\>]{role="bsontype"}, or [long         |
|             | \<Int64\>]{role="bsontype"}.                           |
|             |                                                        |
|             | Returns boolean `false` if the expression resolves to  |
|             | any other                                              |
|             | [BSON type \</reference/mongodb-extended-json\>]{role= |
|             | "doc"},                                                |
|             | `null`, or a missing field                             |
+-------------+--------------------------------------------------------+
| [\$replaceO | Replaces the first instance of a matched string in a   |
| ne]{role="e | given input.                                           |
| xpression"} |                                                        |
+-------------+--------------------------------------------------------+
| [\$replaceA | Replaces all instances of a matched string in a given  |
| ll]{role="e | input.                                                 |
| xpression"} |                                                        |
+-------------+--------------------------------------------------------+

### General Aggregation Improvements

#### `$out`

Starting in MongoDB 4.4, [\$out]{role="pipeline"} can output to a
collection in a different database. In earlier versions,
[\$out]{role="pipeline"} can output to a collection to the same database
where the aggregation is run.

#### `$indexStats`

Starting in MongoDB 4.4 (also available starting in 4.2.4),
[\$indexStats]{role="pipeline"} includes the following fields in its
output:

::: {.container}
  ------------------------------------------------------------------------------------------------------
  Field                                                   Description
  ------------------------------------------------------- ----------------------------------------------
  [shard \<indexStats-output-shard\>]{role="ref"}         Name of the shard, if applicable.

  [spec \<indexStats-output-spec\>]{role="ref"}           Index specification document

  [building \<indexStats-output-building\>]{role="ref"}   A boolean flag that indicates if the index is
                                                          currently being built.
  ------------------------------------------------------------------------------------------------------
:::

#### `$merge`

#### `$planCacheStats` Changes {#4.4-agg-planCachesStats-changes}

#### `$collStats` Changes

Starting in MongoDB 4.4, [\$collStats]{role="pipeline"} accepts the
`queryExecStats` field as an argument document. Providing this field
returns the following fields in the output:

Replica Sets
------------

### Resumable Initial Sync

Starting in MongoDB 4.4, a secondary performing initial sync can attempt
to resume the sync process if interrupted by a *transient* (i.e.
temporary) network error, collection drop, or collection rename. The
sync source must also run MongoDB 4.4 to support resumable initial sync.
If the sync source runs MongoDB 4.2 or earlier, the secondary must
restart the initial sync process as if it encountered a non-transient
network error.

By default, the secondary tries to resume initial sync for 24 hours.
MongoDB 4.4 adds the
[initialSyncTransientErrorRetryPeriodSeconds]{role="parameter"} server
parameter for controlling the amount of time the secondary attempts to
resume initial sync. If the secondary cannot successfully resume the
initial sync process during the configured time period, it selects a new
healthy source from the replica set and restarts the initial
synchronization process from the beginning.

Prior to MongoDB 4.4, the secondary would restart the entire initial
sync if it encountered an error during the process.

### Rollback Directory

### Minimum Oplog Retention Period

To configure the minimum oplog retention period when starting the
[\~bin.mongod]{role="binary"}, either:

-   Add the [storage.oplogMinRetentionHours]{role="setting"} setting to
    the [\~bin.mongod]{role="binary"} [configuration
    file \<configuration-options\>]{role="ref"}.

    *-or-*

-   Add the [\--oplogMinRetentionHours 
    \<mongod \--oplogMinRetentionHours\>]{role="option"} command line
    option.

To configure the minimum oplog retention period on a running
[\~bin.mongod]{role="binary"}, use
[replSetResizeOplog]{role="dbcommand"}. Setting the minimum oplog
retention period while the [\~bin.mongod]{role="binary"} is running
overrides any values set on startup. You must update the value of the
corresponding configuration file setting or command line option to
persist those changes through a server restart.

::: {.seealso}
[/core/replica-set-oplog]{role="doc"}
:::

### Indexes Build Simultaneously on Data-Bearing Replica Set Members

By default, index builds use a commit quorum of all data-bearing voting
members. To start an index build with a non-default commit quorum,
MongoDB 4.4 adds the [commitQuorum
\<createIndexes-cmd-commitQuorum\>]{role="ref"} parameter to
[createIndexes]{role="dbcommand"} or its shell helpers
[db.collection.createIndex()]{role="method"} and
[db.collection.createIndexes()]{role="method"}.

To modify the quorum required for an in-progress index build, MongoDB
4.4 introduces the new [setIndexCommitQuorum]{role="dbcommand"} command.

See [index-operations-replicated-build]{role="ref"} for more
information.

### Replica Set Reconfiguration Changes

#### Changes to `replSetReconfig`

::: {.container}
:::

#### Changes to `replSetGetConfig`

::: {.container}
:::

#### Changes to Replica Configuration Document

::: {.container}
:::

### Preferred Initial Sync Source

Starting in MongoDB 4.4, you can specify the preferred initial sync
source using the [initialSyncSourceReadPreference]{role="parameter"}
parameter. You can only set this parameter on
[\~bin.mongod]{role="binary"} startup, using either the
[setParameter]{role="setting"} configuration file setting or the
[\--setParameter \<mongod \--setParameter\>]{role="option"} command line
option.

[initialSyncSourceReadPreference]{role="parameter"} supports following
read preference modes:

-   [primary]{role="readmode"}
-   [primaryPreferred]{role="readmode"} (Default for voting replica set
    members)
-   [secondary]{role="readmode"}
-   [secondaryPreferred]{role="readmode"}
-   [nearest]{role="readmode"} (Default for newly added *or* non-voting
    replica set members)

If the replica set has disabled [chaining
\<settings.chainingAllowed\>]{role="rsconf"}, the default
[initialSyncSourceReadPreference]{role="parameter"} read preference mode
is [primary]{role="readmode"}.

You cannot specify a tag set or `maxStalenessSeconds` to
[initialSyncSourceReadPreference]{role="parameter"}.

::: {.seealso}
[replica-set-initial-sync-source-selection]{role="ref"}
:::

### Mirrored Reads {#4.4-mirrored-reads}

::: {.container}
Starting in version 4.4, MongoDB provides [mirrored reads
\<mirrored-reads\>]{role="ref"} to pre-warm electable secondary
members\' cache with the most recently accessed data. With mirrored
reads, the primary can mirror a subset of [operations
\<mirrored-reads-supported-operations\>]{role="ref"} that it receives
and send them to a subset of electable secondaries. Pre-warming the
cache of a secondary can help restore performance more quickly after an
election.

::: {.note}
::: {.admonition-title}
Note
:::

The primary\'s response to the client is not affected by the mirror
reads. The mirrored reads are \"fire-and-forget\" operations by the
primary; i.e., the primary does not await the response for the mirrored
reads.
:::
:::

#### Mirrored Reads Parameter

::: {.container}
MongoDB 4.4 adds the following mirrored reads parameter. You can set the
parameter at startup using the [setParameter]{role="setting"}
configuration file setting or the [\--setParameter \<mongod
\--setParameter\>]{role="option"} command line option or at runtime with
[setParameter]{role="dbcommand"} command:

::: {.container}
+-------------+--------------------------------------------------------+
| Parameter   | Description                                            |
+=============+========================================================+
| [mirrorRead | Specifies the `samplingRate` and `maxTimeMS` settings  |
| s]{role="pa | for mirrored reads.                                    |
| rameter"}   |                                                        |
|             | `{ samplingRate: <float>, maxTimeMS: <int> }`          |
|             |                                                        |
|             | A `samplingRate` of `0` turns off mirrored reads.      |
+-------------+--------------------------------------------------------+
:::
:::

#### Mirrored Reads Metrics

::: {.container}
The command [serverStatus]{role="dbcommand"} and its corresponding
[\~bin.mongo]{role="binary"} shell method
[db.serverStatus()]{role="method"} return
[mirroredReads]{role="serverstatus"} if you specify the field\'s
inclusion in the operation:

``` {.sourceCode .javascript}
db.runCommand( { serverStatus: 1, mirroredReads: 1 } )
```

or

``` {.sourceCode .javascript}
db.serverStatus( { mirroredReads: 1 } )
```
:::

Sharded Clusters
----------------

### Refinable Shard Keys

::: {.container}
Starting in 4.4, MongoDB provides the
[refineCollectionShardKey]{role="dbcommand"} command. With the new
command, you can refine a collection\'s shard key by adding a suffix
field or fields to the existing key. Refining a collection\'s shard key
allows for a more fine-grained data distribution and can address
situations where the existing key has led to [jumbo (i.e. indivisible)
chunks \<jumbo-chunks\>]{role="ref"} due to insufficient cardinality.

For example, you may have an existing `orders` collection with the shard
key `{ customer_id: 1 }`. You can change the shard key by adding a
suffix `order_id` field to the shard key so that
`{ customer_id: 1, order_id: 1 }` becomes the new shard key, allowing
data distribution by both `customer_id` and `order_id` fields.

To use the [refineCollectionShardKey]{role="dbcommand"} command, the
sharded cluster must have
[feature compatibility version (fcv) \<view-fcv\>]{role="ref"} of `4.4`.
For more information, see the
[refineCollectionShardKey]{role="dbcommand"} command.

::: {.note}
::: {.admonition-title}
Note
:::

After you refine the shard key, it may be that not all documents in the
collection have the suffix field(s). To populate the missing shard key
field(s), see [shard-key-missing]{role="ref"}.

Before refining the shard key, ensure that all or most documents in the
collection have the suffix fields, if possible, to avoid having to
populate the field afterwards.
:::

In earlier versions, once you select a shard key, you cannot modify the
shard key.
:::

**Missing Shard Keys**

::: {.container}
With the ability to refine a shard key with a suffix, it may be that not
all documents in the collection have the suffix fields. Starting in
version 4.4, documents in a sharded collection can be missing the shard
key fields. In earlier versions, shard key fields must exist in every
document for a sharded collection. For details, see
[shard-key-missing]{role="ref"}.
:::

### Hedged Reads

::: {.container}
To minimize latencies, [\~bin.mongos]{role="binary"} instances, by
default, can use [hedge reads \<mongos-hedged-reads\>]{role="ref"}. With
hedged reads, the [\~bin.mongos]{role="binary"} instances can route read
operations to multiple members per each queried shard and return results
from the first respondent per shard. By default,
[\~bin.mongos]{role="binary"} instances support using hedged reads. To
turn off a [\~bin.mongos]{role="binary"} instance\'s support for hedged
reads, set the [readHedgingMode]{role="parameter"} parameter for the
[\~bin.mongos]{role="binary"}.

Hedged reads are specified per operation as part of the read preference.
Non-`primary` [read preferences
\</core/read-preference\>]{role="doc"} support hedged reads. Read
preference [nearest]{role="readmode"} specifies hedged read by default.

For more information, see:

-   [mongos-hedged-reads]{role="ref"}
-   [replica-set-read-preference-behavior-mongos]{role="ref"}
:::

#### Hedged Read Parameters

::: {.container}
  ------------------------------------------------------------------------------------------------------
  Parameter                                     Description
  --------------------------------------------- --------------------------------------------------------
  [readHedgingMode]{role="parameter"}           Enables or disables [\~bin.mongos]{role="binary"}
                                                instance\'s support for hedged reads.

  [maxTimeMSForHedgedReads]{role="parameter"}   Specifies the maximimum time limit (in milliseconds) for
                                                the additional read sent to [hedge a read operation
                                                \<mongos-hedged-reads\>]{role="ref"}.
  ------------------------------------------------------------------------------------------------------
:::

#### Hedged Read Option for Read Preference

::: {.container}
To specify hedged read for a read preference, MongoDB 4.4 introduces the
[read-preference-hedged-read]{role="ref"}. To set using a MongoDB
driver, refer to the driver [read preference API
documentation\</drivers\>]{role="ecosystem"}.

The following [\~bin.mongo]{role="binary"} shell methods can accept
hedge options to enable hedged read for the specified read preference:

-   [cursor.readPref()]{role="method"}
-   [Mongo.setReadPref()]{role="method"}
:::

#### Hedged Read Metrics

::: {.container}
The command [serverStatus]{role="dbcommand"} and its corresponding
[\~bin.mongo]{role="binary"} shell method
[db.serverStatus()]{role="method"} return
[hedgingMetrics]{role="serverstatus"}.
:::

### `balancerCollectionStatus` Command

::: {.container}
MongoDB 4.4 provides the command
[balancerCollectionStatus]{role="dbcommand"} and the
[\~bin.mongo]{role="binary"} shell helper method
[sh.balancerCollectionStatus()]{role="method"} that return information
about whether the chunks of a sharded collection are balanced (i.e. do
not need to be moved) as of the time the command is run or need to be
moved. With the command, users can verify that initial chunk creation
and migration has finished.
:::

### Improved `mongos` Startup Procedure

Starting with MongoDB 4.4, [\~bin.mongos]{role="binary"} adds the
following new default startup behavior:

-   [\~bin.mongos]{role="binary"} will now preload a sharded cluster\'s
    routing table on startup, rather than doing so on-demand for the
    first incoming client connection.
-   [\~bin.mongos]{role="binary"} will now prewarm its connection pool
    to shard hosts on startup, rather than doing so on-demand for
    incoming client connections.

This behavior results in faster servicing of initial client connections
after a [\~bin.mongos]{role="binary"} instance is started or restarted.
In particular, this allows sites that employ multiple
[\~bin.mongos]{role="binary"} instances to restart them as necessary, or
add new ones, without initial client requests to those instances needing
to wait on connection establishment.

Both routing table preloading and connection pool prewarming are enabled
by default.

MongoDB 4.4 adds the following parameters for controlling this behavior:

-   [loadRoutingTableOnStartup]{role="parameter"}
    -   *Default*: `true` (Enabled)
    -   Enables or disables support for preloading the routing table on
        startup for the [\~bin.mongos]{role="binary"}.
-   [warmMinConnectionsInShardingTaskExecutorPoolOnStartup]{role="parameter"}
    -   *Default*: `true` (Enabled)
    -   Enables or disables support for prewarming the connection pool
        on startup for the [\~bin.mongos]{role="binary"}.
-   [warmMinConnectionsInShardingTaskExecutorPoolOnStartupWaitMS]{role="parameter"}
    -   *Default*: `2000` (2 seconds)
    -   Sets the timeout in milliseconds before client connections to
        the [\~bin.mongos]{role="binary"} are allowed regardless of
        established connection pool size.

### Improved Routing Table Updates

Running [flushRouterConfig]{role="dbcommand"} is no longer required
after executing the [movePrimary]{role="dbcommand"} or
[dropDatabase]{role="dbcommand"} commands. These two commands now
automatically refresh a sharded cluster\'s routing table as needed when
run. Manually issuing the [flushRouterConfig]{role="dbcommand"} command
is still recommended in the cases described under
[flushRouterConfig Considerations\<flushrouterconfig-considerations\>]{role="ref"}.

### Compound Hashed Shard Keys {#4.4-rel-notes-sharding-compound-hashed}

Starting in MongoDB 4.4, you can shard a collection using a compound
shard key with a single hashed field. Prior to 4.4, MongoDB did not
support compound shard keys with a hashed field.

Compound hashed sharding supports features like [zone sharding
\<zone-sharding\>]{role="ref"}, where the prefix (i.e. first) non-hashed
field or fields support zone ranges while the hashed field supports more
even distribution of the sharded data. For example, the following
operation shards a collection on a compound hashed shard key that
supports zoned sharding:

``` {.sourceCode .javascript}
sh.shardCollection(
  "examples.compoundHashedCollection",
  { "fieldA" : 1, "fieldB" : 1, "fieldC" : "hashed" }
)
```

Compound hashed sharding also supports shard keys with a hashed prefix
for resolving data distribution issues related to [monotonically
increasing fields \<shard-key-monotonic\>]{role="ref"} For example, the
following operation shards a collection on a compound hashed shard key
where the hashed field is the shard key prefix:

``` {.sourceCode .javascript}
sh.shardCollection(
  "examples.compoundHashedCollection",
  { "_id" : "hashed", "fieldA" : 1}
)
```

::: {.seealso}
[sharding-hashed-sharding]{role="ref"} ,
[4.4-rel-notes-compound-hashed-index]{role="ref"}
:::

### General Sharded Clusters Improvements

#### Index Consistency Checks

::: {.container}
Starting in MongoDB 4.4, the [config server
\</core/sharded-cluster-config-servers\>]{role="doc"} primary, by
default, checks for index inconsistencies across the shards for sharded
collections. The command [serverStatus]{role="dbcommand"} returns the
field [shardedIndexConsistency]{role="serverstatus"} to report on index
inconsistencies when run on the config server primary.

To configure the index consistency checks, MongoDB provides the
following parameters:

  --------------------------------------------------------------------------------------------------------
  Parameter                                                    Description
  ------------------------------------------------------------ -------------------------------------------
  [enableShardedIndexConsistencyCheck]{role="parameter"}       Enable or disable the index consistency
                                                               checks.

  [shardedIndexConsistencyCheckIntervalMS]{role="parameter"}   The interval at which the config server\'s
                                                               primary checks the index consistency of
                                                               sharded collections.
  --------------------------------------------------------------------------------------------------------
:::

#### Concurrent `removeShard` Operations

::: {.container}
Starting in MongoDB 4.4, you can have more than one
[removeShard]{role="dbcommand"} operation in progress.

In earlier versions, [removeShard]{role="dbcommand"} returns an error if
another [removeShard]{role="dbcommand"} operation is in progress.
:::

#### Shard Key Limit

::: {.container}
Starting in version 4.4, MongoDB removes the 512-byte limit on the shard
key size. For MongoDB 4.2 and earlier, a shard key cannot exceed 512
bytes.
:::

#### Partial Results

::: {.container}
Starting in 4.4, if the [find]{role="dbcommand"} or subsequent
[getMore]{role="dbcommand"} commands return partial results due to the
unavailability of the queried shard(s), the output includes a boolean
flag `partialResultsReturned`.
:::

#### Jumbo Chunk Migration

::: {.container}
:::

#### Automatically Split `system.sessions` Collection

Starting in version 4.4 (and 4.2.7), MongoDB automatically splits the
`system.sessions` collection into at least 1024 chunks and distributes
the chunks uniformly across shards in the cluster.

Projection
----------

Starting in MongoDB 4.4, as part of making
[\~db.collection.find]{role="method"} and
[\~db.collection.findAndModify]{role="method"} projection consistent
with aggregation\'s [\$project]{role="pipeline"} stage,

-   The [\~db.collection.find]{role="method"} and
    [\~db.collection.findAndModify]{role="method"} projection can accept
    aggregation expressions and aggregation syntax. With the use of
    aggregation expressions and syntax, you can project new fields or
    project existing fields with new values.
-   The [\~db.collection.find]{role="method"} and
    [\~db.collection.findAndModify]{role="method"} projection can
    specify embedded fields using the nested form; e.g.
    `{ field: { nestedfield: 1 } }` as well as
    [dot notation \<document-dot-notation\>]{role="ref"}. In earlier
    versions, you can only use the dot notation.

For more information, see:

-   [db.collection.find()]{role="method"}
-   [db.collection.findOneAndDelete()]{role="method"}
-   [db.collection.findOneAndReplace()]{role="method"}
-   [db.collection.findOneAndUpdate()]{role="method"}
-   [db.collection.findAndModify()]{role="method"}

::: {.seealso}
[4.4-compatibility-projection-restrictions]{role="ref"}
:::

### `$meta` Operator[]{#4.4-rel-notes-projection-meta-keyword} {#4.4-rel-notes-agg-meta-keyword}

::: {.container}

`$meta` Keyword Support

:   --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
      Starting in MongoDB 4.4, the [\$meta]{role="expression"} operator adds support for retrieving the `indexKey` metadata. The `indexKey` metadata is for debugging purposes only and not for application logic. See [\$meta]{role="expression"} for more information.
      --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

`{ $meta: "textScore" }` Usage with `find()`

:   +-----------------------------------------------------------------------+
    | Starting in version 4.4, MongoDB makes the following                  |
    | [{ \$meta: \"textScore\" } \<\$meta\>]{role="expression"} changes     |
    | when used with [db.collection.find()]{role="method"}:                 |
    |                                                                       |
    | -   You must specify the [\$text]{role="query"} operator in the query |
    |     predicate to use                                                  |
    |     [{ \$meta: \"textScore\" } \<\$meta\>]{role="expression"}.        |
    | -   You can sort the resulting documents by their search relevance,   |
    |     i.e. [{ \$meta: \"textScore\" } \<\$meta\>]{role="expression"},   |
    |     without also having to project the `textScore`.                   |
    |                                                                       |
    |     | In earlier versions, to include                                 |
    |       [{ \$meta: \"textScore\"  } \<\$meta\>]{role="expression"}      |
    |       expression in the [\~cursor.sort()]{role="method"}, you must    |
    |       also include the same expression in the projection.             |
    |     |                                                                 |
    |                                                                       |
    | -   If you include the                                                |
    |     [{ \$meta: \"textScore\" } \<\$meta\>]{role="expression"}         |
    |     expression in both the projection and sort, i.e.                  |
    |     `db.collection.find(<$text search>, <projection>).sort(<sort>)`,  |
    |     the projection and sort documents can have different field names  |
    |     for the expression.                                               |
    |                                                                       |
    |     | In previous versions of MongoDB, if you include the             |
    |       [{  \$meta: \"textScore\" } \<\$meta\>]{role="expression"} in   |
    |       both the projection and sort, you must specify the same field   |
    |       name in both places.                                            |
    |                                                                       |
    | For more information, see [project-meta-textscore-sort]{role="ref"}.  |
    | For examples of `"textScore"` projections and sorts, see              |
    | [ex-text-search-score]{role="ref"}.                                   |
    +-----------------------------------------------------------------------+
:::

::: {.seealso}
[4.4-compatibility-meta-textscore]{role="ref"}
:::

Transactions
------------

### Transactions Support Collection and Index Creation

Starting in MongoDB 4.4 with
[feature compatibility version (fcv) \<view-fcv\>]{role="ref"} `"4.4"`,
you can:

-   Create collections inside of a
    [multi-document transaction \</core/transactions\>]{role="doc"}.
-   Create indexes inside of a multi-document transaction in the
    following scenarios:
    -   The index is specified on a non-existing collection. In this
        case, the collection is created implicitly.
    -   The index is specified on an empty collection which was created
        earlier in the same transaction.

For more details, see
[transactions-create-collections-indexes]{role="ref"}.

With fcv `"4.2"` or less, operations that affect the database catalog,
such as creating or dropping a collection or an index, are not allowed
in transactions. For details on transaction restrictions, see
[transactions-ops-restricted]{role="ref"}.

::: {.note}
::: {.admonition-title}
Note
:::

You can only run the [\~db.createCollection()]{role="method"} and
[\~db.collection.createIndexes()]{role="method"} methods when using read
concern level of [\"local\"]{role="readconcern"}. If you specify a read
concern level other than [\"local\"]{role="readconcern"}, the
transaction fails.
:::

::: {.seealso}
-   [/core/transactions]{role="doc"}
-   [transactions-operations-ref]{role="ref"}
:::

Security Improvements
---------------------

### New Kerberos Validation Tool `mongokerberos`

MongoDB Enterprise 4.4 provides a new
[\~bin.mongokerberos]{role="binary"} tool for validating your
platform\'s Kerberos configuration for use with MongoDB, and for testing
end-to-end client authentication through Kerberos. When run,
[\~bin.mongokerberos]{role="binary"} returns a report indicating any
issues encountered, and provides potential advice for resolving them.
[\~bin.mongokerberos]{role="binary"} is available in MongoDB Enterprise
only.

### OCSP

Starting in version 4.4, MongoDB enables, by default, the use of OCSP
(Online Certificate Status Protocol) to check for certificate
revocation. The use of OCSP eliminates the need to periodically download
a [Certificate Revocation List (CRL)
\<net.tls.CRLFile\>]{role="setting"} and restart the
[\~bin.mongod]{role="binary"}/[\~bin.mongos]{role="binary"} with the
updated CRL.

In versions 4.0 and 4.2, the use of OCSP is available only through the
use of [system certificate store
\<net.tls.certificateSelector\>]{role="setting"} on Windows or macOS.

::: {.seealso}
-   [/core/security-transport-encryption]{role="doc"}
:::

#### OCSP Stapling/Must Staple

::: {.container}
As part of its OCSP support, MongoDB 4.4 supports the following on
Linux:
:::

#### OCSP Parameters

::: {.container}
MongoDB 4.4 adds the following OCSP parameters. You can set these
parameters at startup using the [setParameter]{role="setting"}
configuration file setting or the
[\--setParameter \<mongod \--setParameter\>]{role="option"} command line
option:
:::

### x.509 Certificates Nearing Expiry Trigger Warnings {#4.4-rel-notes-certificate-expiration-warning}

Starting in MongoDB 4.4,
[\~bin.mongod]{role="binary"}/[\~bin.mongos]{role="binary"} logs a
warning on connection if the presented x.509 certificate expires within
`30` days of the `mongod/mongos` system clock. Specifically, the
following connections to a `mongod` or `mongos` can trigger x.509
certificate expiry warnings:

-   A [\~bin.mongo]{role="binary"} shell or an application using a
    [MongoDB driver \</drivers\>]{role="ecosystem"} establishing a
    [TLS connection \<ssl-clients\>]{role="ref"} *or* performing
    [x.509 client authentication \<x509-client-authentication\>]{role="ref"}
    with a certificate expiring in less than 30 days. (i.e. the
    certificate specified to [mongo \--tlsCertificateKeyFile 
    \<mongo \--tlsCertificateKeyFile\>]{role="option"} or
    [tlsCertificateKeyFile]{role="urioption"}).
-   A [\~bin.mongod]{role="binary"} cluster member performing
    [x.509 membership authentication \<x509-internal-authentication\>]{role="ref"}
    with a certificate expiring in less than 30 days. (i.e. the
    certificate specified to [net.tls.clusterFile]{role="setting"},
    [net.tls.clusterCertificateSelector]{role="setting"},
    [mongod \--tlsClusterFile \<mongod \--tlsClusterFile\>]{role="option"}
    or [mongod \--tlsClusterCertificateSelector 
    \<mongod \--tlsClusterCertificateSelector\>]{role="option"}).
-   A [\~bin.mongos]{role="binary"} cluster member performing
    [x.509 membership authentication \<x509-internal-authentication\>]{role="ref"}
    with a certificate expiring within 30 days. (i.e. the certificate
    specified to (i.e. the certificate specified to
    [net.tls.clusterFile]{role="setting"},
    [net.tls.clusterCertificateSelector]{role="setting"},
    [mongos \--tlsClusterFile \<mongos \--tlsClusterFile\>]{role="option"}
    or [mongos \--tlsClusterCertificateSelector]{role="option"}).

The warning log message resembles the following:

``` {.sourceCode .text}
<Timestamp> W NETWORK [connection] Peer certificate <Certificate Subject Name> expires...
```

Consider proactively renewing client x.509 certificates nearing
expiration to ensure continued connectivity to the cluster.

MongoDB 4.4 adds the
[tlsX509ExpirationWarningThresholdDays]{role="parameter"} parameter for
controlling certificate expiration warning threshold. Set the parameter
to `0` to disable the warning. For complete documentation, see
[tlsX509ExpirationWarningThresholdDays]{role="parameter"}.

### TLS 1.3 Support

On CentOS 8 and RHEL 8, MongoDB 4.4 (as well as versions 4.2, 4.0, and
3.6) support TLS1.3.

Structured Logging
------------------

Starting in MongoDB 4.4, [\~bin.mongod]{role="binary"} /
[\~bin.mongos]{role="binary"} instances now output all log messages in
[structured JSON format
\<log-message-json-output-format\>]{role="ref"}. Log entries are written
as a series of key-value pairs, where each key indicates a log message
field type, such as \"severity\", and each corresponding value records
the associated logging information for that field type, such as
\"informational\".

This includes log output sent to the *file*, *syslog*, and *stdout*
(standard out)
[log destinations \<log-message-destinations\>]{role="ref"}, as well as
the output of the [getLog]{role="dbcommand"} command.

Previously, log entries were output as plaintext.

The following log messages in JSON format indicate that a
[\~bin.mongod]{role="binary"} is listening and ready for connections:

``` {.sourceCode .javascript}
{"t":{"$date":"2020-05-18T20:18:13.533+00:00"},"s":"I",  "c":"NETWORK",  "id":23015,   "ctx":"listener","msg":"Listening on","attr":{"address":"127.0.0.1"}}
{"t":{"$date":"2020-05-18T20:18:13.533+00:00"},"s":"I",  "c":"NETWORK",  "id":23016,   "ctx":"listener","msg":"Waiting for connections","attr":{"port":27001,"ssl":"off"}}
```

Structured logging with key-value pairs allows for efficient log
analysis by automated tools or log ingestion services, and makes
programmatic [log parsing \<log-message-parsing\>]{role="ref"} easier
and more powerful.

For more information on structured logging, including a detailed
examination of log entry components as well as command-line parsing
examples, see [Log Messages \</reference/log-messages\>]{role="doc"}.

Platform Support
----------------

-   MongoDB 4.4 removes support for:
    -   Windows 7 / Server 2008 R2
    -   Windows 8 / Server 2012
    -   Windows 8.1 / Server 2012 R2
    -   macOS 10.12

See [prod-notes-supported-platforms]{role="ref"}.

Mongo Shell
-----------

### Mongo Shell Supports AWS IAM Credentials for Atlas Clusters

Starting in MongoDB 4.4, the [\~bin.mongo]{role="binary"} shell supports
using [AWS IAM
credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
to authenticate to a [MongoDB
Atlas](https://www.mongodb.com/cloud/atlas?tck=docs_server) cluster that
has been configured for AWS IAM authentication.

Authenticating in this manner uses the new `MONGODB-AWS`
[authentication mechanism \<authMechanism\>]{role="urioption"}, and
requires that you provide an AWS access key ID and a secret access key,
which may be specified in the [connection string
\<connection-string-auth-options\>]{role="ref"} or on the command-line
via the [\--username \<mongo \--username\>]{role="option"} and
[\--password
\<mongo \--password\>]{role="option"} options.

Additionally, if you are using an [AWS session
token](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html)
for authentication with temporary credentials when using an
[AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html)
request, or when working with AWS resources that specify this value such
as Lambda, you may provide that session token in the connection string
using the `AWS_SESSION_TOKEN`
[authMechanismProperties]{role="urioption"} value, or on the
command-line via the [\--awsIamSessionToken
\<mongo \--awsIamSessionToken\>]{role="option"} option.

Alternatively, if the AWS access key ID, secret access key, or session
token are defined on your platform using their respective [AWS IAM
environment
variables](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html#envvars-list)
the [\~bin.mongo]{role="binary"} shell will use these environment
variable values to authenticate; you do not need to specify them in the
connection string.

See [Connection String Authentication Options
\<connection-string-auth-options\>]{role="ref"} for usage, and
[Connecting to an Atlas Cluster using MONGODB-AWS
\<connections-string-example-mongodb-aws\>]{role="ref"} for examples.

Tools
-----

### Migration to MongoDB Database Tools Project

Starting in MongoDB 4.4, the documentation for the following tools have
been migrated to the [MongoDB Database Tools
project](https://docs.mongodb.com/database-tools):

+-----------------------------------+-----------------------------------+
| -   [\~bin.mongoimport]{role="bin | -   [\~bin.mongotop]{role="binary |
| ary"}                             | "}                                |
| -   [\~bin.mongoexport]{role="bin | -   [\~bin.mongostat]{role="binar |
| ary"}                             | y"}                               |
| -   [\~bin.mongodump]{role="binar | -   [\~bin.bsondump]{role="binary |
| y"}                               | "}                                |
| -   [\~bin.mongorestore]{role="bi |                                   |
| nary"}                            |                                   |
+-----------------------------------+-----------------------------------+

The MongoDB Database Tools use the Apache License, Version 2.0. See
[mongodb/mongo-tools](https://github.com/mongodb/mongo-tools/) for the
source code.

::: {.note}
::: {.admonition-title}
Note
:::

For documentation on previous versions of the listed tools, reference
that version of the MongoDB server manual.

*Quick links to older documentation:*

-   :v4.2:MongoDB 4.2 Tools \</reference/program/\>
-   :v4.0:MongoDB 4.0 Tools \</reference/program/\>
-   :v3.6:MongoDB 3.6 Tools \</reference/program/\>
:::

### New `mongokerberos` Kerberos Validation Tool

MongoDB Enterprise 4.4 provides a new
[\~bin.mongokerberos]{role="binary"} tool for validating your
platform\'s Kerberos configuration for use with MongoDB, and for testing
end-to-end client authentication through Kerberos. When run,
[\~bin.mongokerberos]{role="binary"} returns a report indicating any
issues encountered, and provides potential advice for resolving them.
[\~bin.mongokerberos]{role="binary"} is available in MongoDB Enterprise
only.

See the [\~bin.mongokerberos]{role="binary"} reference page for more
information.

### `mongoreplay` Removed from MongoDB Packaging

Starting in MongoDB 4.4, `mongoreplay` is removed from MongoDB
packaging. `mongoreplay` and its related documentation are migrated to
the [mongodb-labs](https://github.com/mongodb-labs/mongoreplay) github
project. Projects in `mongodb-labs` are experimental and not officially
supported by MongoDB.

*Quick links to older documentation*

-   :v4.2:mongoreplay 4.2 \</reference/program/mongoreplay\>
-   :v4.0:mongoreplay 4.0 \</reference/program/mongoreplay\>
-   :v3.6:mongoreplay 3.6 \</reference/program/mongoreplay\>

Drivers
-------

### New Drivers

-   The official [MongoDB Rust
    driver](https://docs.mongodb.com/drivers/rust) is now available.
-   The official [MongoDB Swift
    driver](https://docs.mongodb.com/drivers/swift) is now available.

Indexes {#4.4-removed-commands}
-------

### Compound Hashed Indexes {#4.4-rel-notes-compound-hashed-index}

MongoDB 4.4 adds support for creating compound indexes with a single
[hashed field \<index-type-hashed\>]{role="ref"}. MongoDB 4.2 and
earlier only supported single field hashed indexes.

The following operation creates a compound hashed index on `country` and
`_id`:

``` {.sourceCode .javascript}
db.examples.createIndex( { "country" : 1, "_id" : "hashed" } )
```

Compound hashed indexes require [featureCompatibilityVersion
\<view-fcv\>]{role="ref"} set to `4.4`.

::: {.seealso}
[4.4-rel-notes-sharding-compound-hashed]{role="ref"}
:::

### Hidden Indexes

Starting in version 4.4, MongoDB adds the ability to hide or unhide
indexes from the query planner. An index hidden from the query planner
is not evaluated as part of query plan selection.

By hiding an index from the planner, users can evaluate the potential
impact of dropping an index without having to drop the index. If the
impact is negative, the user can unhide the index instead of having to
recreate a dropped index. And because indexes are fully maintained while
hidden, hidden indexes are immediately available for use once unhidden.

For details, see [/core/index-hidden]{role="doc"}.

To support [hidden indexes \</core/index-hidden\>]{role="doc"}, MongoDB
introduces:

-   A new index option `hidden`. The option can be specified in the
    following operations:
    -   [createIndexes  \<cmd-createIndexes-hidden\>]{role="ref"}
        command
    -   [db.collection.createIndex() \<method-createIndex-hidden\>]{role="ref"}
        and
        [db.collection.createIndexes() \<method-createIndexes-hidden\>]{role="ref"}
        methods
    -   [collMod \<index\>]{role="collflag"} command
-   New [\~bin.mongo]{role="binary"} shell helper methods to hide or
    unhide existing indexes:
    -   [db.collection.hideIndex()]{role="method"}
    -   [db.collection.unhideIndex()]{role="method"}

### `dropIndexes` Can Abort In-Progress Index Builds

If an index specified to [dropIndexes]{role="dbcommand"} is still
building, [dropIndexes]{role="dbcommand"} attempts to abort the
in-progress build. Aborting an index build has the same effect as
dropping the built index. Prior to MongoDB 4.4,
[dropIndexes]{role="dbcommand"} would return an error if the collection
had any in-progress index builds. This behavior also applies to the
shell helpers [db.collection.dropIndex()]{role="method"} and
[db.collection.dropIndexes()]{role="method"}.

-   The indexes specified to [dropIndexes]{role="dbcommand"} /
    [\~db.collection.dropIndexes()]{role="method"} must be the entire
    set of in-progress builds associated to a given index builder, i.e.
    the indexes built by a single [createIndexes]{role="dbcommand"} or
    [db.collection.createIndexes()]{role="method"} operation.
-   The index specified to [\~db.collection.dropIndex()]{role="method"}
    must be the only index associated to the index builder, i.e. the
    indexes built by a single [createIndexes]{role="dbcommand"} or
    [db.collection.createIndexes()]{role="method"} operation.

To drop a specific index out of a set of related in-progress builds,
wait until the index builds complete and specify that index to
[dropIndexes]{role="dbcommand"} or its shell helpers.

For more complete documentation, see:

-   [dropIndexes-cmd-index-builds]{role="ref"} for the
    [dropIndexes]{role="dbcommand"} command.
-   [dropIndexes-method-index-builds]{role="ref"} for the
    [\~db.collection.dropIndexes()]{role="method"} method.
-   [dropIndex-method-index-builds]{role="ref"} for the
    [\~db.collection.dropIndex()]{role="method"} method.

### `drop()` Can Abort In-Progress Index Builds

### `dropDatabase` Can Abort In-Progress Index Builds

::: {.seealso}
[index-operations]{role="ref"}.
:::

### Deprecation of `geoHaystack` Index and `geoSearch` Command

Removed Commands
----------------

Networking {#4.4-rel-notes-networking}
----------

### Support for TCP Fast Open {#4.4-rel-notes-tcp-fast-open}

Starting with MongoDB 4.4, [\~bin.mongod]{role="binary"} and
[\~bin.mongos]{role="binary"} support TCP Fast Open (TFO) connections by
default. TFO requires both the client and the `mongod/mongos` host
machines support and enable TFO:

Windows

:   The following Windows operating systems support TFO:

    -   Microsoft Windows Server 2016 or later.
    -   Microsoft Windows 10 Update 1607 or later.

macOS

:   macOS 10.11 (El Capitan) and later support TFO

Linux

:   Linux operating systems running Linux Kernel 3.7 or later can
    support inbound TFO connections.

    Linux operating systems running Linux Kernel 4.11 or later can
    support both inbound and outbound TFO connections.

    Set the value of `/proc/sys/net/ipv4/tcp_fastopen` to enable support
    for inbound and/or outbound TFO connections:

    -   Set to `1` to enable only outbound TFO connections
    -   Set to `2` to enable only inbound TFO connections
    -   Set to `3` to enable inbound and outbound TFO connections.

MongoDB 4.4 adds the following parameters for controlling TFO:

+--------------------+-------------------------------------------------+
| Parameter          | Description                                     |
+====================+=================================================+
| [tcpFastOpenServer | *Default*: `true` (Enabled)                     |
| ]{role="parameter" |                                                 |
| }                  | Enables or disables support for inbound TFO     |
|                    | connections to the `mongod/mongos`              |
+--------------------+-------------------------------------------------+
| [tcpFastOpenClient | *Default*: `true` (Enabled)                     |
| ]{role="parameter" |                                                 |
| }                  | *Linux Operating System Only*                   |
|                    |                                                 |
|                    | Enables or disables support for outbound TFO    |
|                    | connections from the `mongod/mongos`.           |
+--------------------+-------------------------------------------------+
| [tcpFastOpenQueueS | *Default*: `1024`                               |
| ize]{role="paramet |                                                 |
| er"}               | Control the size of the queue of pending TFO    |
|                    | connections.                                    |
+--------------------+-------------------------------------------------+

MongoDB 4.4 adds the following counters to the output of
[serverStatus]{role="dbcommand"} and [db.serverStatus()]{role="method"}:

+--------------------+-------------------------------------------------+
| Counter            | Description                                     |
+====================+=================================================+
| [network.tcpFastOp | *Linux only*                                    |
| en.kernelSetting]{ |                                                 |
| role="serverstatus | Indicates kernel support for TFO.               |
| "}                 |                                                 |
+--------------------+-------------------------------------------------+
| [network.tcpFastOp | Indicates operating system support for incoming |
| en.serverSupported | TFO connections.                                |
| ]{role="serverstat |                                                 |
| us"}               |                                                 |
+--------------------+-------------------------------------------------+
| [network.tcpFastOp | Indicates operating system support for outgoing |
| en.clientSupported | TFO connections.                                |
| ]{role="serverstat |                                                 |
| us"}               |                                                 |
+--------------------+-------------------------------------------------+
| [network.tcpFastOp | Indicates the total number of accepted incoming |
| en.accepted]{role= | TFO connections to the                          |
| "serverstatus"}    | [\~bin.mongod]{role="binary"}/[\~bin.mongos]{ro |
|                    | le="binary"}                                    |
|                    | since the `mongod/mongos` last started.         |
+--------------------+-------------------------------------------------+

A complete discussion of TFO is outside the scope of this documentation.
For more information on TFO, start with the following external
resources:

-   [RFC7413 TCP Fast Open](https://tools.ietf.org/html/rfc7413)
-   [TCP Fast Open
    (en.wikipedia.org)](https://en.wikipedia.org/w/index.php?title=TCP_Fast_Open&oldid=922380898)

Validation
----------

### Support for Background Validation {#4.4-background-validation}

For details, see the [validate]{role="dbcommand"} command and the
[db.collection.validate()]{role="method"} helper.

::: {.seealso}
[4.4-validate-method-signature]{role="ref"}
:::

### `maxValidateMBperSec` Parameter

MongoDB 4.4 adds the [maxValidateMBperSec]{role="parameter"} parameter
to limit the I/O and CPU usage when running background validation. See
[maxValidateMBperSec]{role="parameter"} for details.

### Data Throughput Information

General Improvements
--------------------

### Blocking Sort Limit Increased {#4.4-rel-notes-sort-memory-limit}

If MongoDB cannot use an index or indexes to obtain the sort order for a
given [cursor.sort()]{role="method"} operation, MongoDB must perform a
blocking sort on the data. A blocking sort indicates that MongoDB must
consume and process all input documents to the sort before returning
results. Blocking sorts do not block concurrent operations on the
collection or database.

Prior to MongoDB 4.4, MongoDB returned an error if a blocking sort
operations required more than 32 megabytes of system memory. Starting in
MongoDB 4.4, blocking sort operations increase the limit on system
memory to use for the sort operation to 100 megabytes. For blocking sort
operations which require more than 100 megabytes of system memory,
MongoDB returns an error *unless* the query specifies
[cursor.allowDiskUse()]{role="method"} (*New in MongoDB 4.4*).

For more information on sorting and index use, see
[sort-index-use]{role="ref"}.

::: {.seealso}
| [cursor.allowDiskUse()]{role="method"}
| [Memory Limits on Sort Operations \<Sort Operations\>]{role="limit"}
:::

### `find` Can Use Temporary Files To Support Large Non-Indexed Sorts

MongoDB 4.4 adds a new option [allowDiskUse
\<find-cmd-allowDiskUse\>]{role="ref"} to the [find]{role="dbcommand"}
command. With
[allowDiskUse: true \<find-cmd-allowDiskUse\>]{role="ref"}, the
operation can use temporary files on disk when processing a non-indexed
(\"blocking\") [sort \<find-cmd-sort\>]{role="ref"} operation that
exceeds the 100 megabyte memory limit. Prior to MongoDB 4.4, a
[find]{role="dbcommand"} operation with a blocking sort failed if it
exceeded the memory limit while processing the sort.

For the [db.collection.find()]{role="method"} shell method with
[cursor.sort()]{role="method"}, MongoDB 4.4 adds the
[cursor.allowDiskUse()]{role="method"} cursor modifier.

[allowDiskUse \<find-cmd-allowDiskUse\>]{role="ref"} and
[cursor.allowDiskUse()]{role="method"} have no effect if MongoDB can
satisfy the sort using an [index \<index-sort\>]{role="ref"}, *or* if
the blocking sort requires less than 100 megabytes of memory.

For instructions on enabling
[allowDiskUse \<find-cmd-allowDiskUse\>]{role="ref"} for queries issued
through a MongoDB driver, defer to the documentation for your preferred
[MongoDB 4.4-compatible driver \</drivers\>]{role="ecosystem"}.

::: {.seealso}
| [Memory Limits On Sort Operations \<Sort Operations\>]{role="limit"}
| [sort-index-use]{role="ref"}
:::

### Collection Namespace Limit

Starting in MongoDB 4.4,

### `serverStatus` Output Change

#### Field Name Change

[serverStatus]{role="dbcommand"} returns
[flowControl.locksPerKiloOp]{role="serverstatus"} instead of
[flowControl.locksPerOp]{role="serverstatus"}.

#### New Fields

[serverStatus]{role="dbcommand"} includes the following new fields in
its output:

::: {.container}

Aggregation Metrics

:   +-----------------------------------------------------------------------+
    | | [metrics.aggStageCounters]{role="serverstatus"} *(Also available in |
    |   4.2.6+ and 4.0.19+)*                                                |
    +-----------------------------------------------------------------------+

Connections Metrics

:   +--------------------------------------------------------------+
    | | [connections.exhaustIsMaster]{role="serverstatus"}         |
    | | [connections.awaitingTopologyChanges]{role="serverstatus"} |
    +--------------------------------------------------------------+

Default Read Concern Write Concern Metrics

:   +------------------------------------------------------------------------+
    | | [defaultRWConcern]{role="serverstatus"}                              |
    | | [defaultRWConcern.defaultReadConcern]{role="serverstatus"}           |
    | | [defaultRWConcern.defaultReadConcern.level]{role="serverstatus"}     |
    | | [defaultRWConcern.defaultWriteConcern]{role="serverstatus"}          |
    | | [defaultRWConcern.defaultWriteConcern.w]{role="serverstatus"}        |
    | | [defaultRWConcern.defaultWriteConcern.wtimeout]{role="serverstatus"} |
    | | [defaultRWConcern.updateOpTime]{role="serverstatus"}                 |
    | | [defaultRWConcern.updateWallClockTime]{role="serverstatus"}          |
    | | [defaultRWConcern.localUpdateWallClockTime]{role="serverstatus"}     |
    +------------------------------------------------------------------------+
    | | [metrics.getLastError.default]{role="serverstatus"}                  |
    | | [metrics.getLastError.default.unsatisfiable]{role="serverstatus"}    |
    | | [metrics.getLastError.default.wtimeouts]{role="serverstatus"}        |
    +------------------------------------------------------------------------+

Latch Metrics

:   +----------------------------------------+
    | | [latchAnalysis]{role="serverstatus"} |
    +----------------------------------------+

Mirrored Reads Metrics

:   +---------------------------------------------+
    | | [mirroredReads]{role="serverstatus"}      |
    | | [mirroredReads.seen]{role="serverstatus"} |
    | | [mirroredReads.sent]{role="serverstatus"} |
    +---------------------------------------------+

Query Execution Metrics

:   +-----------------------------------------------------------------------+
    | | [metrics.queryExecutor.collectionScans]{role="serverstatus"}        |
    | | [metrics.queryExecutor.collectionScans.nonTailable]{role="serversta |
    | tus"}                                                                 |
    | | [metrics.queryExecutor.collectionScans.total]{role="serverstatus"}  |
    +-----------------------------------------------------------------------+

Replication Metrics

:   +-------------------------------------------------------------------------+
    | |                                                                       |
    | | [metrics.repl.network.replSetUpdatePosition.num]{role="serverstatus"} |
    | | [metrics.repl.network.getmores.numEmptyBatches]{role="serverstatus"}  |
    | | [metrics.repl.syncSource.numSelections]{role="serverstatus"}          |
    | | [metrics.repl.syncSource.numTimesChoseSame]{role="serverstatus"}      |
    | | [metrics.repl.syncSource.numTimesChoseDifferent]{role="serverstatus"} |
    | | [metrics.repl.syncSource.numTimesCouldNotFind]{role="serverstatus"}   |
    +-------------------------------------------------------------------------+

Network Metrics

:   +-------------------------------------------------------+
    | | [network.numSlowDNSOperations]{role="serverstatus"} |
    | | [network.numSlowSSLOperations]{role="serverstatus"} |
    +-------------------------------------------------------+

Security Metrics

:   +-------------------------------------------------------------+
    | | [security.authentication.mechanisms]{role="serverstatus"} |
    +-------------------------------------------------------------+

Sharding Metrics

:   +-----------------------------------------------------------------------+
    | | [shardedIndexConsistency]{role="serverstatus"}                      |
    | | [shardingStatistics.rangeDeleterTasks]{role="serverstatus"}         |
    | | [shardingStatistics.unfinishedMigrationFromPreviousPrimary]{role="s |
    | erverstatus"}                                                         |
    +-----------------------------------------------------------------------+
:::

### `replSetGetStatus` Output Change

[replSetGetStatus]{role="dbcommand"} returns the following new fields:

+-----------------------------------------------------------------------+
| -   [\~replSetGetStatus.tooStale]{role="data"}                        |
| -   [\~replSetGetStatus.votingMembersCount]{role="data"}              |
| -   [\~replSetGetStatus.writableVotingMembersCount]{role="data"}      |
| -   [initialSyncStatus.syncSourceUnreachableSince \<replSetGetStatus. |
| initialSyncStatus.syncSourceUnreachableSince\>]{role="data"}          |
| -   [initialSyncStatus.currentOutageDurationMillis \<replSetGetStatus |
| .initialSyncStatus.currentOutageDurationMillis\>]{role="data"}        |
| -   [initialSyncStatus.totalTimeUnreachableMillis \<replSetGetStatus. |
| initialSyncStatus.totalTimeUnreachableMillis\>]{role="data"}          |
| -   [initialSyncAttempts.rollBackId \<replSetGetStatus.initialSyncSta |
| tus.initialSyncAttempts\>]{role="data"}                               |
| -   [initialSyncAttempts.operationsRetried \<replSetGetStatus.initial |
| SyncStatus.initialSyncAttempts\>]{role="data"}                        |
| -   [initialSyncAttempts.totalTimeUnreachableMillis \<replSetGetStatu |
| s.initialSyncStatus.initialSyncAttempts\>]{role="data"}               |
+-----------------------------------------------------------------------+

### db.auth() Can Prompt for Password {#4.4-rel-notes-db-auth-passwordPrompt}

Starting in MongoDB 4.4, the [\~bin.mongo]{role="binary"} shell method
[db-auth-syntax-username-password]{role="ref"} prompts for the password
if you do not pass in the password or the
[passwordPrompt()]{role="method"} method for the `<password>`.

### Support for `$natural` Sort on Views

### Support for Diagnostic Backtrace Generation

Starting in MongoDB 4.4, [\~bin.mongod]{role="binary"} and
[\~bin.mongos]{role="binary"} processes running on Linux will now log a
backtrace for each of their running threads upon receipt of a `SIGUSR2`
signal. This backtrace can be analyzed for diagnostic information or
provided to MongoDB support as needed. This functionality is currently
available only on the `x86_64` architecture. For more information on
using this feature, see [sigusr2-diagnostic-backtrace]{role="ref"}.

### Container-aware FTDC Reporting

Starting in MongoDB 4.4, [FTDC \<ftdc-stub\>]{role="ref"} now reports
utilization data for a [\~bin.mongod]{role="binary"} running in a
container from the perspective of the container, as opposed to the host
operating system. See [ftdc-stub]{role="ref"} for more information.

### Updated `ulimit` Startup Warning

Starting in MongoDB 4.4, [\~bin.mongod]{role="binary"} will log a
startup warning if a platform\'s configured `ulimit` value for number of
open files is under `64000`. Previously, a warning would only be logged
if this value was under `1000`. See
[recommended-ulimit-settings]{role="ref"} for more information.

### New `replanReason` Database Profiler Output Field

MongoDB 4.4 adds the
[replanReason\<system.profile.replanReason\>]{role="data"} field to
[database profiler output \</tutorial/manage-the-database-profiler\>]{role="doc"}
and [diagnostic log messages \</reference/log-messages\>]{role="doc"}.
The `replanReason` field contains the reason the query system evicted a
[cached plan \<query-plans-query-optimization\>]{role="ref"}.

### `dbStats` and `collStats` Output

The [dbStats]{role="dbcommand"} command and its
[\~bin.mongo]{role="binary"} shell helper [db.stats()]{role="method"}
return:

-   [\~dbStats.totalSize]{role="data"}, which is the sum of
    [\~dbStats.storageSize]{role="data"} and
    [\~dbStats.indexSize]{role="data"}.

The [collStats]{role="dbcommand"} command, its
[\~bin.mongo]{role="binary"} shell helper
[db.collection.stats()]{role="method"}, and the
[\$collStats]{role="pipeline"} aggregation stage return:

-   [\~collStats.totalSize]{role="data"}, which is the sum of
    [\~collStats.storageSize]{role="data"} and
    [\~collStats.totalIndexSize]{role="data"}.
-   [\~collStats.freeStorageSize]{role="data"}, which is the amount of
    storage available for reuse.

### Delete and Hint

Starting in MongoDB 4.4, the [delete]{role="dbcommand"} command and the
associated [\~bin.mongo]{role="binary"} shell methods
[db.collection.deleteOne()]{role="method"} and
[db.collection.deleteMany()]{role="method"} can accept a `hint` argument
to specify the index to use.

Additionally, starting in MongoDB 4.4, when a
[findAndModify]{role="dbcommand"} command has `remove` set to `true`,
the command can accept a `hint` argument to specify the index to use.

See:

-   [delete]{role="dbcommand"}
-   [db.collection.deleteOne()]{role="method"}
-   [db.collection.deleteMany()]{role="method"}
-   [findAndModify]{role="dbcommand"}

### JavaScript Execution on `mongos`

Starting in MongoDB 4.4, MongoDB allows JavaScript execution on
[\~bin.mongos]{role="binary"} instances. To disable JavaScript execution
on a [\~bin.mongos]{role="binary"} instance:

-   Set [security.javascriptEnabled]{role="setting"} configuration
    option to false, or
-   Specify the
    [\--noscripting \<mongos \--noscripting\>]{role="option"}
    command-line option.

Earlier versions of MongoDB do not allow JavaScript execution on
[\~bin.mongos]{role="binary"} instances.

### Global Default Read and Write Concern

::: {.admonition}
Requires `featureCompatibilityVersion` 4.4+

Each [\~bin.mongod]{role="binary"} in the replica set or sharded cluster
*must* have [featureCompatibilityVersion \<set-fcv\>]{role="ref"} set to
at least `4.4` to configure global default read and write concern.
:::

Starting in MongoDB 4.4, replica sets and sharded clusters support
configuring global default read and write concern settings. Clients
which do not explicitly specify a given read or write concern setting
inherit the corresponding global default setting.

To configure default global default read or write concern, MongoDB adds
the [setDefaultRWConcern]{role="dbcommand"} administrative command. For
replica sets, issue the command against the [primary]{role="term"}
member. For sharded clusters, issue the command from a
[\~bin.mongos]{role="binary"}.

To retrieve the global default read or write concern settings, MongoDB
adds the [getDefaultRWConcern]{role="dbcommand"} administrative command.

#### Read Concern Provenance

Starting in MongoDB 4.4, read concern objects may include a `provenance`
field, indicating where the read concern originated.

The following table shows the possible read concern `provenance` values
and their significance:

If a read operation is logged or profiled, the operation entry contains
the read concern object, including the `provenance` field.

#### Write Concern Provenance

Starting in MongoDB 4.4, write concern objects may include a
`provenance` field, indicating where the write concern originated.

The following table shows the possible write concern `provenance` values
and their significance:

If a write operation is logged or profiled, the operation entry contains
the write concern object, including the `provenance` field.

### `currentOp` Output

-   The [\$currentOp]{role="pipeline"} includes
    [\~\$currentOp.dataThroughputAverage]{role="data"} and
    [\~\$currentOp.dataThroughputLastSecond]{role="data"} information
    when reporting on validate operations in progress.
-   The [currentOp]{role="dbcommand"} command includes
    [\~currentOp.dataThroughputAverage]{role="data"} and
    [\~currentOp.dataThroughputLastSecond]{role="data"} information when
    reporting on validate operations in progress.

### New KMIP Connection Parameters for `mongod`

MongoDB 4.4 Enterprise introduces two new configuration settings to
enhance the initial connection to a KMIP server, as part of Kerberos
authentication:

#### Connection Retries

To control the number of times the [\~bin.mongod]{role="binary"} retries
a failed initial connection to the KMIP server:

-   Set the [security.kmip.connectRetries]{role="setting"} configuration
    option, or
-   Specify the [mongod \--kmipConnectRetries]{role="option"}
    command-line option.

#### Connection Timeout

To control the timeout, in milliseconds, to wait for the initial
response from the KMIP server before giving up, or retrying:

-   Set the [security.kmip.connectTimeoutMS]{role="setting"}
    configuration option, or
-   Specify the [mongod \--kmipConnectTimeoutMS]{role="option"}
    command-line option.

These settings are available in MongoDB Enterprise only.

### Improvements to `explain` Results

Starting in version 4.4 (and 4.2.2, 4.0.14, 3.6.16):

-   [Explain results \</reference/explain-results/\>]{role="doc"} for
    commands run on sharded clusters include a top-level
    [serverInfo \<serverInfo\>]{role="ref"} object for the
    [\~bin.mongos]{role="binary"} in addition to the `serverInfo`
    objects returned for each shard.
-   [Explain results \</reference/explain-results/\>]{role="doc"}
    include the [serverInfo \<serverInfo\>]{role="ref"} object when
    [\~explain.queryPlanner.optimizedPipeline]{role="data"} is `true`.
    In previous versions of MongoDB, `explain` results would
    occasionally not include the `serverInfo` object when
    [\~explain.queryPlanner.optimizedPipeline]{role="data"} was `true`.

::: {.seealso}
[Explain results \</reference/explain-results/\>]{role="doc"}.
:::

[Changes Affecting Compatibility \</release-notes/4.4-compatibility\>]{role="doc"}
----------------------------------------------------------------------------------

Some changes can affect compatibility and may require user actions. For
a detailed list of compatibility changes, see
[/release-notes/4.4-compatibility]{role="doc"}.

Upgrade Procedures {#4.4-upgrade}
------------------

::: {.admonition}
Feature Compatibility Version

To upgrade from 4.2 deployment, the 4.2 deployment must have
`featureCompatibilityVersion` set to `4.2`. To check the version:

``` {.sourceCode .javascript}
db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )
```

For specific details on verifying and setting the
`featureCompatibilityVersion` as well as information on other
prerequisites/considerations for upgrades, refer to the individual
upgrade instructions:

-   [/release-notes/4.4-upgrade-standalone]{role="doc"}
-   [/release-notes/4.4-upgrade-replica-set]{role="doc"}
-   [/release-notes/4.4-upgrade-sharded-cluster]{role="doc"}
:::

If you need guidance on upgrading to 4.4, [MongoDB offers major version
upgrade
services](https://www.mongodb.com/products/consulting?tck=docs_server)
to help ensure a smooth transition without interruption to your MongoDB
application.

Report an Issue
---------------

To report an issue, see
<https://github.com/mongodb/mongo/wiki/Submit-Bug-Reports> for
instructions on how to file a JIRA ticket for the MongoDB server or one
of the related projects.

::: {.hidden}
::: {.toctree}
/release-notes/4.4-compatibility /release-notes/4.4-upgrade-standalone
/release-notes/4.4-upgrade-replica-set
/release-notes/4.4-upgrade-sharded-cluster /release-notes/4.4-downgrade
:::
:::
