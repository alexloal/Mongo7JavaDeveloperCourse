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

# Using `$project`, `$count` and `$set` Stages in a MongoDB Aggregation Pipeline

## `$project` 
- Determines the output document shape.
- Projection similar to `find()` operations.
- Should be the last stage to format the output.
- 1 to include.
- 0 to exclude.
- new_value specified for new fields and existing fields being given a new value.

```json
$project {
  <field>: 1,
  <field>: 0,
  ...,
  <field>: <new_value>
}
```
Example:
```json
db.zips.aggregate([
  {
    $project: {
      state: 1,
      zip: 1,
      population: "$pop",
      _id: 0
    }
  }
])
```

## `$set`
- Adds or modifies fields in the pipeline.
- Useful when we want to change existing fields in pipeline or add new ones to be used in upcoming pipelines stages.

```json
$set {
  <field>: <value>,
  <field>: <value>,
  ...,
  <field>: <value>
}
```
Example:

```json
db.zips.aggregate([
  {
    $set: {
      pop_2022: {
        $round: {
          $multiply:[ 1.0031, "$pop"] 
        }
      }
    }
  }
])
```

# `$count`
- Counts documents in the pipeline.
- Returns the total document count.

```json
db.zips.aggregate([
  {
    $count: "total_zips"
  }
])
Output -> [  {    total_zips: 29367  }]
```
## Practice 1
You are now connected to an Atlas cluster and to the `bird_data` database with a collection of sightings. There is a lot of data in each document, but we want to return only a list of the time of the sighting and the common name of the bird that was sighted.

Use the `sightings` collection in this lab.

1. Create an aggregation pipeline on the `sightings` collection. (Forgot the command or aggregation stages? Check the hint below!)
2. Create a `$project` stage that just shows us the "date" and "species_common" field. Project out the "_id" field.
3. Run your aggregation pipeline, and see the list of sightings!

```json
db.sightings.aggregate([{
    $project: {
        date: 1,
        species_common: 1,
        _id: 0
    }
}])
```
## Practice 2
You are now connected to an Atlas cluster and to the `bird_data` database with a collection of information about bird species. In the future, we’ll add new animals to the database and collection. Before we do that, we have to add a `class` field and set that field to `bird` in all of the existing documents in the collection

Use the `birds` collection in this lab.

1. Create an aggregation pipeline on the `birds` collection. (Forgot the command or aggregation stages? Check thehint below!)
2. Create a new field called `class` populated with the class of each of these animals, bird.
3. Run your aggregation pipeline, and see that the `class` field has been added and set to "bird".
```json
db.birds.aggregate([{
    $set:{
        class: "bird"
    }
}])
```

## Practice 3
You are now connected to an Atlas cluster and to the `bird_data` database with a collection of sightings. We want to see the total number of sightings of Eastern Bluebirds in 2022.

Use the `sightings` collection in this lab.

1. Create an aggregation pipeline on the `sightings` collection. (Forgot the command or aggregation stages? Check the hint below!)
2. Create a stage that finds matches where `species_common` is "Eastern Bluebird" and where the `date` field is a value between January 1, 2022 0:00, and January 1, 2023 0:00.
3. Create a stage to count how many sightings that includes.
4. Run your aggregation pipeline, and see how many sightings of Eastern Bluebirds took place in 2022.

```json
db.sightings.aggregate([
    { 
        $match:{
            species_common:"Eastern Bluebird",
            date:{$gt:ISODate("2022-01-01T00:00:00.0Z")},
            date:{$lt: ISODate("2023-01-01T00:00:00.0Z")}
        }
    },
    {
        $count: "total"
    }
])
```

## Quiz 1
**What is the main difference between `$set` and `$project`? (Select one.)**

d. `$set` is used to create or change values of new or existing fields. `$project` can be used to create or change the value of fields, but it can also be used to specify which fields to show in the documents in the aggregation pipeline.

Correct!

`$set` and `$project` can both create and assign values to fields, but only `$project` can be used to reshape the data.

**What does the `$count` stage return? (Select one.)**

a. A single document with one field that contains the value set to the number of documents at this stage in the aggregation pipeline.


The `$count` stage returns a document with a field named in the parameters with the value set to the number of documents at this stage in the aggregation pipeline.

# Using the `$out` Stage in a MongoDB Aggregation Pipeline
- Writes the documents that are returned by an aggregation pipeline into a collection.
- Must be the last stage.
- Creates a new collection if it does not already exist.
- If collection exists, `$out` replaces the existing collection with new data.

```json
$out: {
  db: "<db>",
  coll: "<newcollection>"
}
```

```json
{ $out: "<newcollection>" }
```

## Practice 1
You are now connected to an Atlas cluster. 
You have a database called `bird_data` with a collection of sightings. 
We're starting to put together our end-of-the-year report on how many birds were seen. We want to put the sightings records in their own collection so that we can manipulate the data later. 
To do that, you'll use the aggregation stage `$out` to create a new collection.

Use the `sightings` collection in this lab.

1. First, create an aggregation pipeline, which will contain two stages. (Forgot the method for aggregation? Check the hint below!)
2. Create a stage that filters by matching records of sightings that took place in 2022.
3. Add a stage that outputs the filtered data to a new collection with the name `sightings_2022`.
4. Run your aggregation pipeline.
5. Run `db.sightings_2022.findOne()` to see if the new collection was created!

```json
db.sightings.aggregate([
    {
        $match: {
            date:{$gte:ISODate('2022-01-01T00:00:00.000Z')},
            date:{$lt:ISODate('2023-01-01T00:00:00.000Z')},
        }
    },
    {
        $out:"sightings_2022"
    }
    ]
)
```

## Quiz 1
** What does the $out stage do? (Select one).**
Creates a new collection that contains the documents in this stage of the aggregation pipeline.
Correct!

The `$out` stage outputs the documents in the aggregation pipeline to a new collection.

## Quiz 2
** What happens if you set the $out stage to output to a collection that already exists? (Select one.)**
a. The existing collection is erased and replaced with the outputted documents.

Users must be careful not to overwrite any collections unintentionally when specifying the name of the collection to output the documents to.