# Introduction to MongoDB aggregation

## Aggregation framework 
- It is used to build multi-stage queries.
- Collection and summary of data.

## Stage
- One of the built-in methods that can be completed on the data, but does not permanently alter it.
- A single operation on the data.
- `$match` Filters for data that matches criteria.
- `$group` Groups documents based on criteria.
- `$sort` Puts the documents in a specified order.

### Examples:
```json
$match: {
  "Student_id": 1234
}
```
```json
$project: {
  total: { $sum: ["$quiz", "$homework"]}
}
```

```json
$set {
  defaultUserName: {
    $concat: ["$first_name", "$last_name"]
  }
}
```

## Aggregation Pipeline
- A series of stages completed on the data in order.

## Operations
- Filtered.
- Sorted.
- Grouped.
- Transformed.

## Annotation

```json
db.collection.aggregate([
  {
    $stage1: {
      { expression1 },
      { expression2 }...
    },
    $stage2: {
      { expression1 }...
    }
  }
])
```

## Quiz 1
Which of the following tasks cannot be completed with an aggregation pipeline? (Select one.)

Finding data from outside sources
Correct!

You cannot use aggregation to find data from outside sources.

## Quiz 2
Which command performs an aggregation operation by using an aggregation pipeline? (Select one.)

`aggregate()`
Correct!

`aggregate()` performs an aggregation operation by using an aggregation pipeline. `aggregate()` takes an array of aggregation stages to form the pipeline. An aggregation function is written as `db.collection.aggregate()`.

# Using `$match` and `$group` Stages in a MongoDB Aggregation Pipeline

## `$match`
- Filter for documents matching criteria.
- Place as early as possible in the pipeline so it can use indexes.
- Reduces the number of documents.
- Less processing required.

```json
db.zips.aggregate([
  { $match: {"state": "CA"} }
])
```

## `$group`
- Create a single document for each distinct value.
- Groups documents by a group key.
- Output is one document for each unique value of the group key.

```json
{
  $group: {
    _id: <expression>, // Group key
    <field>: { <accumulator> : <expression> }
  }
}
```

```json
{
  $group: {
    _id: "city", // Group key
    "totalZips": { "$count": {} }
  }
}
```
```json
db.zips.aggregate([
{   
   $match: { 
      state: "CA"
    }
},
{
   $group: {
      _id: "$city",
      totalZips: { $count : { } }
   }
}
])
```

## Practice 1
You have a database called bird_data with a collection of sightings. We want to use this data to find out where we should go to see our favorite bird, Eastern Bluebirds. We’ll use the location coordinates (latitude and longitude) and the number of sightings in each location to identify the best places to view the Eastern Bluebirds.

You are now connected to an Atlas cluster and to the bird_data database. Use the sightings collection in this lab.

1. First, create an aggregation pipeline, which will contain two stages. (Forgot the method for aggregation? Check the hint below!)

2. Create a `$match` stage that filters for documents about our target bird with the species_common value, `"Eastern Bluebird"`

3. Create a `$group` stage that groups documents based on the latitude and longitude of the sighting, stored in `the location.coordinates` field.

4. Within the groups, create a field to show the $count of how many documents are in each group, called `"number_of_sightings"`.

5. Run your aggregation pipeline, and find out where to look for Eastern Bluebirds!

6. Once you complete this lab, compare your answer to the correct answer in the Review and Solved Code section, then select Next.

```json
db.sightings.aggregate([
    {
        $match: {
            species_common:"Eastern Bluebird"
        }
    },
    {
        $group:{
            _id: "$location.coordinates",
            number_of_sightings: { $count : {}}
        }
    }
])
```

## Quiz 1
You have a collection that contains zip codes in the United States. Here’s a sample document from this collection:
```json
_id: ObjectId('5c8eccc1caa187d17ca6eea2')
city: "EVERGREEN"
zip: "36401"
loc: Object
   y: 31.458009
   x: 86.925771 
pop: 8556
state: "AL"
```
What will the output of this aggregation pipeline be? (Select one.)

```json
db.zips.aggregate([
{
   $match: { "state": "CA" }
},
{
   $group: { "_id": "$zip" }
}
])
```
One document for each zip code located in California `(CA)`
Correct!

First, the `$match` stage finds all documents where the state is California `(CA)`. 
Next, the `$group` stage groups documents according to a group key. 
The output of this stage is one document for each unique value of this group key. 
In this example, the output is one document for each zip code because the group key is the zip code.

## Quiz 2
You have a collection that contains zip codes in the United States. Here’s a sample document from this collection:
```json
_id: ObjectId('5c8eccc1caa187d17ca6eea2')
city: "EVERGREEN"
zip: "36401"
loc: Object
   y: 31.458009
   x: 86.925771 
pop: 8556
state: "AL"
```
What will the output of this aggregation pipeline be? (Select one.)

```json
db.zips.aggregate([
{
   $match: { "state": "TX" }
},
{
   $group: { "_id": "$city" }
}
])
```
One document for each city located in Texas `(TX)`
Correct!

First, the `$match` stage finds all documents where the state is Texas `(TX)`. 
Next, the `$group` stage groups documents according to a group key.
The output of this stage is one document for each unique value of this group key. 
In this example, the output is one document for each city because the group key is the city.

# Using `$sort` and `$limit` Stages in a MongoDB Aggregation Pipeline

## `$sort`
- Sorts all input documents and passes them through pipeline in sorted order.
- `1` Ascending order, `-1` Descending order.

```json
{
    $sort: {
        "field_name": 1
    }
}
```

## `$limit`
- Limits the number of documents that are passed on to the next aggregation stage.

```json
{
  $limit: 5
}
```

```json
db.zips.aggregate([
  { $sort: { pop: -1 } },
  { $limit:  5 }
])
```

## Practice 1
You are now connected to an Atlas cluster. You have a database called `bird_data` with a collection of sightings. 
We want to use this data to find the birds that are sighted furthest North.

Use the sightings collection in this lab.

1. Create an aggregation pipeline. (Forgot the command or aggregation stages? Check the hint below!)

2. Use a `$sort` stage to sort the data from North to South. To do this, use `location.coordinates.1`, noting the schema is `{ location: { coordinates: [x, y] } }`, where the highest latitude value is the furthest North. The y in the schema represents latitude.

3. Use a `$limit` stage to limit the number of documents so that you're shown the `top 4` documents.

4. Run your aggregation pipeline, and find out which birds have been sighted way up North!

5. Once you complete this lab, compare your answer to the correct answer in the Review and Solved Code section, then select Next.
```json
db.sightings.aggregate([
    { $sort: { "location.coordinates.0": -1 }},
    { $limit: 4}
])
```

## Quiz 1
You have a collection that contains zip codes in the United States. Here’s a sample document from this collection:
```json
_id: ObjectId('5c8eccc1caa187d17ca6eea2')
city: "EVERGREEN"
zip: "36401"
loc: Object
y: 31.458009
x: 86.925771
pop: 8556
state: "AL"
```
What will the output of this aggregation pipeline be? (Select one.)
```json
db.zips.aggregate([
  { $group: { "_id": "$pop" }},
  { $sort: { _id: -1 }}
])
```
One document for the population of each zip code, sorted in descending order
Correct!

The `$group` stage groups documents according to a group key. 
The output of this stage is one document for each unique value of this group key. 
In this example, the output is one document per each zip code’s population. 
Each document contains only the population value of each zip as the `_id`. 
Next, the `$sort` stage sorts all input documents and returns them to the pipeline in sorted order. 
`$sort` takes a document that specifies in the field(s) to sort by and the respective sort order, 
`1` for ascending and `-1` for descending. 
In this example, we sorted by population in descending order meaning we return the documents in order based on the largest population.

## Quiz 2
You have a collection that contains all the zip codes in the United States. Here’s a sample document from this collection:
```json
_id: ObjectId('5c8eccc1caa187d17ca6eea2')
city: "EVERGREEN"
zip: "36401"
loc: Object
y: 31.458009
x: 86.925771
pop: 8556
state: "AL"
```

What will the output of this aggregation pipeline be? (Select one.)
```json

db.zips.aggregate([
  {$group: { "_id": "$pop" }},
  {$sort: { _id: -1 }},
  {$limit: { 10 }}
])
```
`10` documents, each containing the population of a zip code as the `_id`, sorted by population in descending order
Correct!

The sort order of the `$sort` stage is `-1`, which means that the `$sort` stage will sort documents in `descending` order based on population. 
The `$limit` stage limits the number of documents that are returned to `10`.