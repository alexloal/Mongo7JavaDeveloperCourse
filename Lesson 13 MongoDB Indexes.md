# MongoDB Indexes

## Lesson 1: Using MongoDB Indexes in Collections

### What indexes are

- Special data structures
- Store small portion of the data
- Ordered and easy to search efficiently
- Point to the document identity

![img_3.png](Images/img_3.png)

### Costs of using indexes
#### Indexes improve query performance:
- Speed up queries
- Reduce disk I/O
- Reduce resources required
- Support equality matches and range-based operations and return sorted results

#### Without indexes
- MongoDB reads all documents (collection scan)
- Sorts results in memory

#### With indexes
- MongoDB only fetches the documents identified by the index based on the query
- Return results faster
- By default, there is one default index per collection, which includes only the _id field

1. Every query should use an index.
2. If we insert or update documents, we need to update the index data structure.
3. Write performance can degrade if we have too many indexes in a collection.
4. Delete unnecessary or redundant indexes.

### Most common index types
#### How indexes can improve performance
1. Single field
2. Compound
3. Both indexes are multikey indexes operate on an array field

![img_4.png](Images/img_4.png)
![img_5.png](Images/img_5.png)

Both queries can use this index

### Quiz 1

**Which of the following statements about indexes are correct? (Select all the that apply.)**

a. Indexes are data structures that improve performance, support efficient equality matches and range-based query operations, and can return sorted results
Indexes are data structures that improve performance, support efficient equality matches and range-based query operations, and can return sorted results. Indexes achieve this by allowing MongoDB to perform only the work necessary to return the data that is requested, rather than scanning the entire collection.

c. Indexes are used to make querying faster for users. One of the easiest ways to improve the performance of a slow query is create indexes on the data that is used most often.
Indexes help make querying faster for users by only scanning the indexes to find the data that is requested.

### Quiz 2

**Which of the following statements about indexes are true? (Select one.)**
b. Indexes improve query performance at the cost of write performance.
Indexes improve query performance at the cost of write performance. For most use cases, this tradeoff is acceptable. Indexes should be used on data that is frequently queried or on queries that are infrequent but costly in terms of computational resources.

### Practice 1
![img_6.png](Images/img_6.png)

## Lesson 2: Creating a Single Field Index in MongoDB

### Create a single-field index by using `createIndex()`

```json lines
db.customers.createIndex({
  birthdate: 1
})
```

![img_7.png](Images/img_7.png)

![img_8.png](Images/img_8.png)

### Creating a single index in a unique field.

Add `{unique:true}` as a second, optional, parameter in `createIndex()` to force uniqueness in the index field values. 
Once the unique index is created, any inserts or updates including duplicated values in the collection for the index field/s will fail.

```json lines
db.customers.createIndex({
  email: 1
},
{
  unique:true
})
```
- MongoDB only creates the unique index if there is no duplication in the field values for the index field/s.

### Enforce uniqueness
- The unique constraint helps with the queries and prevents duplication of the email values.

### Identify indexes by using `getIndexes()`

![img_9.png](Images/img_9.png)

- Show the indexes that are being used.

### Determine the indexes being used in a query by using `explain()`

- To determine if any of these indexes are being used in a query, we use the `explain` command

![img_10.png](Images/img_10.png)

- The `winningPlan` shows the index used in this query.

![img_11.png](Images/img_11.png)

- Another query

![img_12.png](Images/img_12.png)

- The winning plan only support the sort in the query.
- In the `rejectedPlan` it's displayed the query supported.

![img_13.png](Images/img_13.png)
![img_14.png](Images/img_14.png)

- Another query
![img_15.png](Images/img_15.png)

- This is not supported by any indexes, the winning plan is a collection scan.
![img_16.png](Images/img_16.png)

- Use `explain()` in a collection when running a query to see the Execution plan. 
This plan provides the details of the execution stages (`IXSCAN`, `COLLSCAN`, `FETCH`, `SORT`, etc.).

1. The `IXSCAN` stage indicates the query is using an index and what index is being selected.
2. The `COLLSCAN` stage indicates a collection scan is perform, not using any indexes.
3. The `FETCH` stage indicates documents are being read from the collection.
4. The `SORT` stage indicates documents are being sorted in memory.

```json lines
db.customers.explain().find({
  birthdate: {
    $gt:ISODate("1995-08-01")
    }
  })
```
```json lines
db.customers.explain().find({
  birthdate: {
    $gt:ISODate("1995-08-01")
    }
  }).sort({
    email:1
    })
```

### Quiz 1
**What is a single field index? (Select one.)**

a. An index that supports efficient querying against one fieldYour
A single field index is an index that supports efficient querying against a single field. 
By default, all collections have a single field index on the `_id` field, but users can define additional indexes that support important queries. 
A single field index is also a multikey index if the value of the field is an array.

**You have a collection of customer details. The following is a sample document from the collection:**
```json lines
{
  "_id": { "$oid": "5ca4bbcea2dd94ee58162a6a" },
  "username": "hillrachel",
  "name": "Katherine David",
  "address": "55711 Janet Plaza Apt. 865\nChristinachester, CT 62716",
  "birthdate": { "$date": { "$numberLong": "582848134000" } },
  "email": "timothy78@hotmail.com",
  "Accounts": [
    { "$numberInt": "462501" },
    { "$numberInt": "228290" },
    { "$numberInt": "968786" },
    { "$numberInt": "515844" },
    { "$numberInt": "377292" }
  ],
  "tier_and_details": {}
}
```
**You create a single field index on the email field, with the unique constraint set to true:**
```json lines
db.customers.createIndex({email:1}, {unique:true}) 
```
**What would happen if you attempt to insert a new document with an email that already exists in the collection? (Select one.)**
d. MongoDB will return a duplicate key error, and the document will not be inserted.
Unique indexes ensure that indexed fields do not store duplicate values. 
In this example, MongoDB will return a duplicate key error if you attempt to insert a new document with an email that already exists in the collection, 
as the `unique` constraint was set to `true`.

### Practice 1
### Get Indexes for a MongoDB Collection
In this lab, you will retrieve an array of indexes that hold information about the indexes on the `transfers` collection.

Before you begin, please note that you are now connected to an Atlas cluster and the `bank` database. 

Use the `transfers` collection for this lab.

db.transfers.getIndexes()

### Create an Index with an Equality Constraint

In this lab, we will create a single field index on the `accounts` collection of the `bank` database. 
We will create this single field index with a unique constraint on a field to ensure that there can only exist one document in the collection with a given value for the indexed field.

Before you begin, please note that you are now connected to an Atlas cluster and the bank database. 
Use the `accounts` collection for this lab.

#### Lab Instructions
1. In this lab, you will be working with the `accounts` collection. 
Once you are using the accounts collection, run `db.accounts.findOne()` to view the first document in the collection to understand the structure of the document. You should see a document similar to the following:
```json lines
{
  _id: ObjectId("62d6e04ecab6d8e130497487"),
  account_id: 'MDB829000996',
  account_holder: 'Kasper Sørensen',
  account_type: 'checking',
  balance: Decimal128("3373.98000000000"),
  transfers_complete: [
    'TR266268604',
    'TR399880553',
    'TR277358549',
    'TR695865388',
    'TR573014677'
  ]
}
```
2. Create a single field index with a unique constraint on the `account_id` field to ensure that only one document has a given value for the indexed field. Forgot the command? Check the hints below!

**db.accounts.createIndex({'account_id':1},{unique:true})**

![img_17.png](Images/img_17.png)

### Use `explain()` to Verify that Indexes are Working

In this lab, we will use a command to view the `winningPlan` for a query that uses an index. 
A `winningPlan` is a document that contains information about the query and the method that was used to execute the query.

Before you begin, please note that you are now connected to an Atlas cluster and the bank database. 
Use the accounts collection for this lab.

### Lab Instructions
1. In this lab, you will use the `accounts` collection. First, create a query that finds a document with the `account_id` field equal to `MDB829000996`.
 ```json lines
   db.accounts.find({'account_id':'MDB829000996'});
 ```
2. Add the `explain()` method to your query to view the `winningPlan` (Forgot the command? Check the hints below!):

```json lines
db.accounts.explain().find({'account_id':'MDB829000996'});
```
```json lines
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'bank.accounts',
    indexFilterSet: false,
    parsedQuery: { account_id: { '$eq': 'MDB829000996' } },
    queryHash: '0535B3BE',
    planCacheKey: '0535B3BE',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'COLLSCAN',
      filter: { account_id: { '$eq': 'MDB829000996' } },
      direction: 'forward'
    },
    rejectedPlans: []
  },
  command: {
    find: 'accounts',
    filter: { account_id: 'MDB829000996' },
    '$db': 'bank'
  },
  serverInfo: {
    host: 'atlas-d3opcw-shard-00-01.3xfvk.mongodb.net',
    port: 27017,
    version: '7.0.15',
    gitVersion: '57939cc60865b0ce431c7e08c2589fa266a1a740'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600,
    internalQueryFrameworkControl: 'trySbeRestricted'
  },
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1734271521, i: 7 }),
    signature: {
      hash: Binary.createFromBase64('YYAgQB6FjLq9TD8trCT6oAuijrA=', 0),
      keyId: Long('7388445949397303297')
    }
  },
  operationTime: Timestamp({ t: 1734271521, i: 7 })
}
```

## Lesson 3: Creating a Multikey Index in MongoDB

### How MongoDB works with array fields in an index

![img_18.png](Images/img_18.png)

- It will be created a multikey index using these three fields.
- Multikey indexes can index:
  1. Primitives
  2. Subdocuments
  3. Subarrays
- Can be single field or compound index
- There is a limitation of only one array field per index

![img_19.png](Images/img_19.png)

Here's an example of a search using a MultiKey index.

## Create a Single field Multikey Index
Use `createIndex()` to create a new index in a collection. 
Include an object as parameter that contains the array field and sort order. 
In this example `accounts` is an array field.
```json lines
db.customers.createIndex({
accounts: 1
```
})

## View the Indexes used in a Collection
Use `getIndexes()` to see all the indexes created in a collection.
```json lines
db.customers.getIndexes()
```

### Check if an index is being used on a query
Use `explain()` in a collection when running a query to see the Execution plan. This plan provides the details of the execution stages (IXSCAN , COLLSCAN, FETCH, SORT, etc.).

- The `IXSCAN` stage indicates the query is using an index and what index is being selected.
- The `COLLSCAN` stage indicates a collection scan is perform, not using any indexes.
- The `FETCH` stage indicates documents are being read from the collection.
- The `SORT` stage indicates documents are being sorted in memory.
```json lines
db.customers.explain().find({
accounts: 627788
})
```

### Quiz 1

**What is a multikey index? (Select one.)**

b. An index where one of the indexed fields contains an array
Correct.

Multikey indexes support efficient queries against array fields by creating an index key for each element in the array. This allows MongoDB to search for the index key of each element in the array rather than scan the entire array, which results in dramatic performance gains in your queries.

### Quiz 2
**What is the maximum number of array fields per multikey index? (Select one.)**
a. 1
Correct! The maximum number of array fields per multikey index is 1. If an index has multiple fields, only one of them can be an array.

### Practice 1
### Creating Multikey Indexes and Using explain()

In this lab, we will create a multikey single field index on the accounts collection of the bank database. Additionally, we will verify that the index was created and actively used in queries using the explain() method on a query that uses the index.

Before you begin, please note that you are now connected to an Atlas cluster and the bank database. Use the accounts collection for this lab.

Lab Instructions
Create a multikey single field index on the transfers_complete field. After creating the index, you should see the following output in the terminal (Forgot the command? Check the hints below!):

shell

copy
transfers_complete_1
Use the explain() method to view the winningPlan for a query that finds a specific completed_transfers array element and ensure that the multikey single field index you created is being used. The element we want to find is the transfers_complete field set to TR617907396.

```json lines
db.accounts.createIndex({transfers_complete:1})

db.accounts.explain().find({'transfers_complete':'TR617907396'})
```

```json lines
{
  explainVersion: '1',
  queryPlanner: {
    namespace: 'bank.accounts',
    indexFilterSet: false,
    parsedQuery: { transfers_complete: { '$eq': 'TR617907396' } },
    queryHash: '54865789',
    planCacheKey: '9642D82D',
    maxIndexedOrSolutionsReached: false,
    maxIndexedAndSolutionsReached: false,
    maxScansToExplodeReached: false,
    winningPlan: {
      stage: 'FETCH',
      inputStage: {
        stage: 'IXSCAN',
        keyPattern: { transfers_complete: 1 },
        indexName: 'transfers_complete_1',
        isMultiKey: true,
        multiKeyPaths: { transfers_complete: [ 'transfers_complete' ] },
        isUnique: false,
        isSparse: false,
        isPartial: false,
        indexVersion: 2,
        direction: 'forward',
        indexBounds: { transfers_complete: [ '["TR617907396", "TR617907396"]' ] }
      }
    },
    rejectedPlans: []
  },
  command: {
    find: 'accounts',
    filter: { transfers_complete: 'TR617907396' },
    '$db': 'bank'
  },
  serverInfo: {
    host: 'atlas-d3opcw-shard-00-02.3xfvk.mongodb.net',
    port: 27017,
    version: '7.0.15',
    gitVersion: '57939cc60865b0ce431c7e08c2589fa266a1a740'
  },
  serverParameters: {
    internalQueryFacetBufferSizeBytes: 104857600,
    internalQueryFacetMaxOutputDocSizeBytes: 104857600,
    internalLookupStageIntermediateDocumentMaxSizeBytes: 104857600,
    internalDocumentSourceGroupMaxMemoryBytes: 104857600,
    internalQueryMaxBlockingSortMemoryUsageBytes: 104857600,
    internalQueryProhibitBlockingMergeOnMongoS: 0,
    internalQueryMaxAddToSetBytes: 104857600,
    internalDocumentSourceSetWindowFieldsMaxMemoryBytes: 104857600,
    internalQueryFrameworkControl: 'trySbeRestricted'
  },
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1737132317, i: 1 }),
    signature: {
      hash: Binary.createFromBase64('Sg4JdSx5S1CBtju93DpIDRkHdAU=', 0),
      keyId: Long('7424410961856954383')
    }
  },
  operationTime: Timestamp({ t: 1737132317, i: 1 })
}
```

## Lesson 4: Working with Compound Indexes in MongoDB

### What compound indexes are

- Index on multiple fields
- Can be a multikey index if it includes an array field
- Maximum of one array field per index
- Support queries that match on the prefix of the index fields

Explaining better this last point

![img_20.png](Images/img_20.png)

The indexes are ordered structures
- The order of the fields in a compound index matters
- Follow this order: Equality, Sort, Range
- The sort order of the field values in the index matters

### Equality
- Test exact matches on single field
- Should be placed first in a compound index
- Reduces query processing time
- Retrieves fewer documents

![img_21.png](Images/img_21.png)

### Sort
- Determines the order of results
- Index sort eliminates the need for in-memory sorts
- Sort order is important if query results are sorted by more than 1 field and they mix sort orders

![img_22.png](Images/img_22.png)

### Range
- Range filters scan fields
- Range and Sort should be placed after equality in the index to avoid in-memory sort or filtering.

![img_23.png](Images/img_23.png)

### How to create a compound index

Executing this query:
![img_25.png](Images/img_25.png)

We can have the following results:
![img_26.png](Images/img_26.png)

All results match the filter criteria.
Now, we are going to explain the query to understand what MongoDB is doing to retrieve the data.

![img_27.png](Images/img_27.png)

- MongoDB performs an index scan over one of the existing indexes -> `birthdate_1` (yellow)
- Then it executes a fetch that uses a filter looking for the active customer -> `FETCH` (green)
- It performs a memory sort based on birthdate in descending order and name in ascending order -> `SORT` (red)

We can improve the performance of the query by creating an index that better supports it.

It creates an index with Equality first followed by Sort.

![img_28.png](Images/img_28.png)

- The `active` field will be first because we query by using equality on this field.
- `Birthdate` and `name` are placed next because we sort by these fields.
- The sort order of these fields matters because the mix descending and ascending orders in the query.
- Following the same patter as in the query.

Verify the new index it will run the explain query again.

![img_29.png](Images/img_29.png)

- The winning plan shows the new index (yellow)
- A fetch stage is performed based on the index results, resulting only in the necessary documents from the collection. (green)
- In most cases, the fetch stage can be avoided if we only project fields that are in the index.

### How to use a compound index to sort or cover queries
To the previous query, we will add a projection in the find command to return only name and birthdate excluding the default _ID field.

![img_31.png](Images/img_31.png)

Now use the explain query to review the execution plan.

![img_32.png](Images/img_32.png)

- The compound index is being used (yellow)
- There is no fetch stage because the index covers the projection (green)
- This execution is faster than the one with no projection because there is no extra step to read the documents.

![img_24.png](Images/img_24.png)

- If a query projects additional fields that we don't use to filter or sort by,
we can include those fields at the end of the field list when we create the index.
- This way we use the index to cover the query and avoid fetching the documents.

## Working with Compound Indexes

### Create a Compound Index

Use `createIndex()` to create a new index in a collection. Within the parentheses of `createIndex()`, include an object 
that contains two or more fields and their sort order.

```json lines
db.customers.createIndex({
  active:1, 
  birthdate:-1,
  name:1
})
```

### Order of Fields in a Compound Index

The order of the fields matters when creating the index and the sort order. 
It is recommended to list the fields in the following order: Equality, Sort, and Range.

- Equality: field/s that matches on a single field value in a query
- Sort: field/s that orders the results by in a query
- Range: field/s that the query filter in a range of valid values

The following query includes an equality match on the active field, a sort on birthday (descending) and name (ascending), and a range query on birthday too.

```json lines
db.customers.find({
  birthdate: {
    $gte:ISODate("1977-01-01")
    },
    active:true
    }).sort({
      birthdate:-1, 
      name:1
      })
```
Here's an example of an efficient index for this query:
```json lines
db.customers.createIndex({
  active:1, 
  birthdate:-1,
  name:1
})
```
### View the Indexes used in a Collection

Use `getIndexes()` to see all the indexes created in a collection.
```json lines
db.customers.getIndexes()
```

### Check if an index is being used on a query

Use `explain()` in a collection when running a query to see the Execution plan. This plan provides the details of the 
execution stages (`IXSCAN`, `COLLSCAN`, `FETCH`, `SORT`, etc.). Some of these are:

- The `IXSCAN` stage indicates the query is using an index and what index is being selected.
- The `COLLSCAN` stage indicates a collection scan is perform, not using any indexes.
- The `FETCH` stage indicates documents are being read from the collection.
- The `SORT` stage indicates documents are being sorted in memory.

```json lines
db.customers.explain().find({
  birthdate: {
    $gte:ISODate("1977-01-01")
    },
  active:true
  }).sort({
    birthdate:-1,
    name:1
    })
```

### Cover a query by the Index

An Index covers a query when MongoDB does not need to fetch the data from memory since all the required data is already 
returned by the index.

In most cases, we can use projections to return only the required fields and cover the query. Make sure those fields in 
the projection are in the index.

By adding the projection `{name:1,birthdate:1,_id:0}` in the previous query, we can limit the returned fields to only 
`name` and `birthdate`. These fields are part of the index and when we run the `explain()` command, 
the execution plan shows only two stages:

- `IXSCAN` - Index scan using the compound index 
- `PROJECTION_COVERED` - All the information needed is returned by the index, no need to fetch from memory

```json lines
db.customers.explain().find({
  birthdate: {
    $gte:ISODate("1977-01-01")
    },
  active:true
  },
  {name:1,
    birthdate:1, 
    _id:0
  }).sort({
    birthdate:-1,
    name:1
    })
```

### Quiz 1

**What is a compound index? (Select one.)**

C. An index that contains references to multiple fields within a document
Correct!

A compound index is an index that contains references to multiple fields within a document. Compound indexes are created 
by adding a comma-separated list of fields and their corresponding sort order to the index definition.

### Quiz 2

**What is the recommended order of fields in a compound index? (Select one.)**

C. Equality, Sort, Range
Correct! The recommended order of indexed fields in a compound index is Equality, Sort, and Range. Optimized queries use 
the first field in the index, Equality, to determine which documents match the query. The second field in the index, Sort, 
is used to determine the order of the documents. The third field, Range, is used to determine which documents to include 
in the result set.

### Practice 1

Creating a Compound Index and Using `explain()`

In this lab, we will create a compound index on the `accounts` collection of the `bank` database. Additionally, we will 
verify that the index was created and actively used in queries using the `explain()` method.

![img_33.png](Images/img_33.png)

Lab Instructions

1. Using the In the `mongosh`, create a compound index using the `account_holder`, `balance` and `account_type` fields 
on the `accounts` collection where all fields are sorted in `ascending` order.

```json lines
bank> db.accounts.createIndex({'account_holder':1, 'balance':1, 'account_type':1})
account_holder_1_balance_1_account_type_1
```
2. Use the `explain` method to confirm that the index is being used. Be sure to look at the winningPlan field in the 
output as this contains information about the index that is being used. Here is a sample query you can use to confirm 
the index is used:

```json lines
db.accounts.explain().find(
  { account_holder: "Andrea", balance: { $gt: 5 } },
  { account_holder: 1, balance: 1, account_type: 1, _id: 0 }
).sort({ balance: 1 });
```

![img_34.png](Images/img_34.png)

We can see that the query was using a compound index. We can see that the compound index is being used by the presence 
of the `indexBounds` property. Additionally, you can see that the `multikey` property is set to `false`. This means that 
the compound index that we created is present and being used. You may also notice that the `FETCH` stage is not present 
in the `winningPlan`. This is because we are only projecting fields that are in the index, so the `FETCH` stage can be 
avoided.

## Lesson 5: Deleting MongoDB Indexes

### Impact of deleting an index

- Indexes improve performance of the queries.
- Indexes have a write cost. Every time it is inserted new documents or update them, the index keys need to be updated.
- Too many indexes in a collection can affect the system performance.
- We should delete unused or redundant indexes.

- Before remove the index:
- Make sure the index is not being used.
- Deleting an index that's the only index supporting a query will affect the performance of the query.
- We can delete any of the indexes in a collection except the default index on `_id`.
- Recreating an index takes time and resources.
- If we are not sure about the index, hide the index instead of deleting it.
- To hide the index, we use the `db.collection.hideIndex(<index>)` command.
- MongoDB does not use hidden indexes in queries but continues to update their keys.

![img_35.png](Images/img_35.png)

The index `username_1` in the collection becomes redundant.

### How to delete an index

First, to get all indexes in the collection.

![img_36.png](Images/img_36.png)

- The best practice to hide the index before deleting it.
- This avoids having to recreate it later if we realize the index was needed for a query.
- To hide an index can be specified by the index name or key.

![img_37.png](Images/img_37.png)

- We can use the `dropIndex()` method on the collection.

![img_38.png](Images/img_38.png)

- The number of elements indexed was 5 (grey)
- The drop index was succeeded (yellow)

![img_39.png](Images/img_39.png)

- We can remove an index using atlas UI.

If we want to delete multiple indexes we can use `db.collection.dropIndexes()`
- If we don't specify the indexes it will be deleted all the indexes expect the `_id` index.
- We can specify 1 index `db.collection.dropIndexes('indexName')` 
- We can specify more than 1 index `db.collection.dropIndexes(['index1', 'index2', ...])`

### Deleting an Index

### View the Indexes used in a Collection

Use `getIndexes()` to see all the indexes created in a collection. There is always a default index in every collection 
on `_id` field. This index is used by MongoDB internally and cannot be deleted.

```json lines
db.customers.getIndexes()
```

### Delete an Index

Use `dropIndex()` to delete an existing index from a collection. Within the parentheses of `dropIndex()`, include an 
object representing the index key or provide the index name as a string.

Delete index by name:
```json lines
db.customers.dropIndex(
  'active_1_birthdate_-1_name_1'
)
```
Delete index by key:
```json lines
db.customers.dropIndex({
  active:1,
  birthdate:-1, 
  name:1
})
```

### Delete Indexes

Use `dropIndexes()` to delete all the indexes from a collection, with the exception of the default index on `_id`.

```json lines
db.customers.dropIndexes()
```
The `dropIndexes()` command also can accept an array of index names as a parameter to delete a specific list of indexes.
```json lines
db.collection.dropIndexes([
  'index1name', 'index2name', 'index3name'
  ])
```

### Quiz 1

**What are the ramifications of deleting an index that is supporting a query? (Select one.)**
b. The performance of the query will be negatively affected
Correct!

The performance of the query will be negatively affected by the deletion of the only index that is currently supporting 
that query. Indexes generally improve the performance and time efficiency of queries by reducing the number of times 
that the database needs to be accessed.

### Quiz 2
**You have a collection of customer details. The following is a sample document from this collection:**
```json lines
{
  "_id": { "$oid": "5ca4bbcea2dd94ee58162a6a" },
  "username": "hillrachel",
  "name": "Katherine David",
  "address": "55711 Janet Plaza Apt. 865\nChristinachester, CT 62716",
  "birthdate": { "$date": { "$numberLong": "582848134000" } },
  "email": "timothy78@hotmail.com",
  "Accounts": [
    { "$numberInt": "462501" },
    { "$numberInt": "228290" },
    { "$numberInt": "968786" },
    { "$numberInt": "515844" },
    { "$numberInt": "377292" }
  ],
  "tier_and_details": {}
}
```
**You have an index on the `email` field. Here’s the command you used to create the index:**
```json lines
db.customers.createIndex({email:1})
```
**Before deleting it, you want to assess the impact of removing this index on the performance of the query. 
To do this, which command should you use? (Select one.)**

D. hideIndex()
Correct.

The `hideIndex()` command hides an index. By hiding an index, you'll be able to assess the impact of removing the index 
on query performance. MongoDB does not use hidden indexes in queries but continues to update their keys. This allows you 
to assess if removing the index affects the query performance and unhide the index if needed. Unhiding an index is faster 
than recreating it. In this example, you would use the command `db.customers.hideIndex({email:1})`.

### Practice 1

## Delete an Index

In this lab, we will verify that a single field is using an index, and then delete that index. After deleting, we'll use 
the `explain()` method to ensure that the index is no longer being used.

Before you begin, please note that you are now connected to an Atlas cluster and the `bank` database. Use the `accounts` 
collection for this lab.

### Lab Instructions
1. Run the following `explain()` command to verify that the `account_holder` field is using an `index` while being 
sure to take a note of the `indexName`:
```json lines
db.accounts.explain().find({ account_holder: "Puja Barbier" })
```
![img_40.png](Images/img_40.png)

2. Delete the index on the `account_holder` field. (Forgot the command? Check the hints below!)
```json lines
db.accounts.dropIndex('account_holder_1')
```
![img_41.png](Images/img_41.png)

3. Run the following `explain()` method again to verify that the `account_holder` field is no longer using an index:
```json lines
db.accounts.explain().find({ account_holder: "Puja Barbier" })
```
![img_42.png](Images/img_42.png)