# Sorting and Limiting Query Results in MongoDB

## Cursor 

Pointer to the result set of a query.

`find()` returns a cursor to the documents that match that query.

### Sort

`find()` -> Can be sorted by a field

`db.companies.find({category_code:"music"}).sort({name:1})` -> Sorting by name ascending order (-1) descending.

Can be sorted by multiple fields at the same time by passing additional fields.

Capital letters are first followed by the lowercase letters.

This can be changed in the options of the sort method.

#### Syntax:
`db.collection.find(<query>).sort(<sort>)`

#### Example:
`// Return data on all music companies, sorted alphabetically from A to Z.`
`db.companies.find({ category_code: "music" }).sort({ name: 1 });`

`// Return data on all music companies, sorted alphabetically from A to Z. Ensure consistent sort order`
`db.companies.find({ category_code: "music" }).sort({ name: 1, _id: 1 });`

### Limit

Limiting the number of results can enhance performance of the query avoiding unnecessary data processing.

`db.companies.find({category_code:"music"}).sort({number_of_employees:-1}).limit(3)` -> limit to 3 results


### Projection

`db.companies.find({category_code:"music"}, {name:1}).sort({name:1})` -> return the results of the query displaying only the name of the entries.

`db.companies.find({category_code:"music"}, {name:1, number_of_employees:1}).sort({number_of_employees:-1}).limit(3)` -> Displaying top 3 companies showing name and number of employees.

#### Syntax:
`db.companies.find(<query>).limit(<number>)`

#### Example:
`// Return the three music companies with the highest number of employees. Ensure consistent sort order.`
`db.companies.find({ category_code: "music" }).sort({ number_of_employees: -1, _id: 1 }).limit(3);`

### Practice 1

## Return Query Results in Ascending Order

In this activity, you will return query results in ascending order.

### Lab Instructions

You are now connected to an Atlas cluster and the sample_supplies database.

You will use the sales collection. (Suggestion: run `db.sales.findOne()` to return a sample document and review the structure of the data in this this collection.)

    1. Return the data on all sales, ordered by date from oldest to newest.(Forgot the command? Check the hints below!)


`db.sales.find().sort({saleDate:1});`
`db.sales.find({}, {saleDate:1, _id:1}).sort({saleDate:1});`

### Practice 2

## Return Query Results in Descending Order

In this activity, you will return query results in descending order.

### Lab Instructions

You are now connected to an Atlas cluster and the sample_supplies database.

You will use the sales collection.

    1. Return the data on all sales made online using a coupon, ordered by date from the most recent to the oldest.

`db.sales.find({couponUsed:true}).sort({saleDate:-1});`

### Practice 3

## Return a Limited Number of Sorted Results

In this activity, you will return query results sorted by most recent and specify the maximum number of documents to return.

### Lab Instructions

You are now connected to an Atlas cluster and the sample_supplies database.

You will use the sales collection.

    1. Return the data on the three most recent sales made from the London store that included one or more of the following items: a laptop, a backpack or printer paper.

`db.sales.find({storeLocation:"London", "items.name":{$in:["laptop","backpack","printer paper"]}}).sort({saleDate:-1}).limit(3);`

### Quiz 1

Using the `inspections` collection within the `sample_training` database, you need to find all inspections that were passed. Your manager has requested that you organize this data by the certificate number in ascending order. Which query should you use? (Select one).

`db.inspections.find( { result : "Pass" }).sort( {certificate_number: 1});`

### Quiz 2

You are considering creating a new membership tier for your bike sharing service for users who take long trips. Using the `trips` collection within the `sample-training` database, you need to find the trips, taken by subscribers, with the longest trip duration. Return the top 5 results in descending order. Which query should you use? (Select one.)

`db.trips.find( { usertype: "Subscriber"}).sort( { tripduration:  - 1 }).limit(5)`

# Returning Specific Data from a Query in MongoDB

## Projection

_id is included in the projections by default.

It's configured in the second parameter of the find method.

`db.inspections.find({sector: "Restaurant - 818"}, {business_name:1, result:1})`

The value 1 -> specified an inclusion approach, only these specific fields are added ot the result.

`db.inspections.find({sector: "Restaurant - 818"}, {business_name:1, result:1, _id:0})`

The value 0 -> exclude the field _id of the projection.

Inclusion & exclusion statements can't be combined in projections.

`_id` field is an exception.

We can include or exclude fields but not both with the exception of the _id field.

`db.inspections.find({result:{$in:["Pass","Warning"]}}, {date:0, "address.zip":0})`

#### Syntax:
`db.collection.find( <query>, <projection> )`

Include a Field

#### Syntax:
`db.collection.find( <query>, { <field> : 1 })`

#### Example:
`// Return all restaurant inspections - business name, result, and _id fields only`
`db.inspections.find({ sector: "Restaurant - 818" }, { business_name: 1, result: 1 })`

Exclude a Field

#### Syntax:
`db.collection.find(query, { <field> : 0, <field>: 0 })`

#### Example:
`// Return all inspections with result of "Pass" or "Warning" - exclude date and zip code`
`db.inspections.find({ result: { $in: ["Pass", "Warning"] } }, { date: 0, "address.zip": 0 })`

While the _id field is included by default, it can be suppressed by setting its value to 0 in any projection.

`// Return all restaurant inspections - business name and result fields only`
`db.inspections.find({ sector: "Restaurant - 818" },{ business_name: 1, result: 1, _id: 0 })`

## Return Selected Fields, Including the _id Field

In this activity, you will write a query that returns only selected fields from matching documents, including the `_id` field.

### Lab Instructions

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the `sales` collection for this activity. (Suggestion: run `db.sales.findOne()` to return a sample document and review the structure of the data in this collection.)

    1. Query for all sales at the Denver store. Return only the sale date, store location, purchase method and _id fields.

`db.sales.find({storeLocation: "Denver"}, {saleDate:1, storeLocation:1, purchaseMethod:1});`

## Return Selected Fields, Excluding the _id Field

In this activity, you will write a query that returns only selected fields from matching documents, excluding the `_id` field.

### Lab Instructions

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the `sales` collection for this activity.

    1. Find the data on sales to customers less than 30 years old in which the customer satisfaction rating was greater than three. Return only the customer's age and satisfaction rating, the sale date and store location. Do not include the _id field. 

`db.sales.find({"customer.age": {$lt: 30}, "customer.satisfaction": {$gt: 3}}, {"customer.age":1, "customer.satisfation":1, saleDate:1, storeLocation:1, _id:0});`

## Return All Fields Except Those Explicitly Excluded

In this activity, you will write a query that returns all fields except those explicitly excluded using a projection.

### Lab Instructions

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the `sales` collection for this activity.

    1. Find data on all sales from the Seattle and New York stores. Return all data except the purchase method, customer information, and whether a coupon was used. 

`db.sales.find({storeLocation: {$in: ["Seattle","New York"]}}, {purchaseMethod:0, customer:0, couponUsed:0});`

### Quiz 1

**Which of the following statements are true about a projection document? (Select all that apply.)**

1. We can include fields in our results by setting their values to 1 in the projection document.
2. We can exclude fields from our results by setting their values to 0 in the projection document.
3. We can either include or exclude fields in the results, but not both. The `_id` is the exception to this rule.

### Quiz 1

If we don’t want to return the `_id` field, we can add it to the projection document and set it to which of the following values? (Select all that apply.)

1. 0

# Counting Documents in a MongoDB Collection

## Syntax
`db.collection.countDocuments(<query>, <options>)` -> count documents in this collection.
`<query>` -> filter of documents.
`<options>` -> the counting behavior.

`db.trips.countDocument();` -> count all documents in the collection

`db.trips.countDocuments({tripDuration:{$gt: 120}, usertype: "Subscriber"})` -> count documents of this filter

## Count Documents

Use `db.collection.countDocuments()` to count the number of documents that match a query.`countDocuments()` takes two parameters: a query document and an options document.

### Syntax
`db.collection.countDocuments( <query>, <options> )`

### Examples:
`// Count number of docs in trip collection`
`db.trips.countDocuments({})`

`// Count number of trips over 120 minutes by subscribers`
```json lines
db.trips.countDocuments({ tripduration: { $gt: 120 }, usertype: "Subscriber" })
```


## Count All Documents in a Collection

In this activity, you will count all documents in a collection.

### Lab Instructions

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the sales collection for this activity. (Suggestion: run `db.sales.findOne()` to return a sample document and review the structure of the data in this this collection.)

    1. Find the total number of documents in the sales collection.

`db.sales.countDocuments();`

## Count All Documents That Match a Query (1)

In this activity, you will count all documents that match a query made with an equality constraint.

### Lab Instructions

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the sales collection for this activity.

    1. Find the number of sales made using a coupon at the Denver location.

`db.sales.countDocuments({couponUsed:true, storeLocation:"Denver"});`

## Count All Documents That Match a Query (2)

In this activity, you will count all documents that match a query made with an array operator and a comparison operator.

### Lab Instructions

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the sales collection for this activity.

    1. Find the number of sales that included a laptop that cost less than $600.

`db.sales.countDocuments({items: {$elemMatch: {name: "laptop", price: {$lt: 600}}}});`

### Quiz 1

Which of the following statements are true about the `countDocuments()` collection method? (Select all that apply.)

1. The method takes a query parameter, which selects the documents to be counted.
2. We can use the method to count all documents in a collection.

### Quiz 2

What can we expect to be returned by running `db.inspections.countDocuments({})`? (Select one.)

This command returns the total number of documents in the inspections collection.

## Conclusions

