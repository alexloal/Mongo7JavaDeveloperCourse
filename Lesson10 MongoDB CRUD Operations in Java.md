# Working with MongoDB Documents in Java

## BSON

BSON is the data format that MongoDB uses to organize and store data.

* Optimized for storage, retrieval and transmission across the wire.
* It's more secure than plan text JSON.
* Supports more data types than JSON.

### Quiz 1

How can we represent BSON documents in a Java application?

Document Class -> One way to represent BSON documents is to use the `Document` class. 
The `Document` class offers a flexible representation of a BSON document.

### Quiz 2

What command is used to instantiate a document with fields and values?

append

# Inserting a Document in Java Applications

## insertOne()

Inserts a single document into a collection.

insertOne returns InsertOneResult object with the IDs of the inserted document.

```java
MongoDatabase database = mongoClient.getDatabase("sample_training");
MongoCollection<Document> collection = database.getCollection("inspections");

Document inspection = new Document("_id", new ObjectId())
    .append("id", "10021-2015-ENFO")
    .append("certificate_number", 9278806)
    .append("business_name", "ATLIXCO DELI GROCERY INC.")
    .append("date", Date.from(LocalDate.of(2015, 2, 20).atStartOfDay(ZoneId.systemDefault()).toInstant()))
    .append("result", "No Violation Issued")
    .append("sector", "Cigarette Retail Dealer - 127")
    .append("address", new Document().append("city", "RIDGEWOOD").append("zip", 11385).append("street", "MENAHAN ST").append("number", 1712));

InsertOneResult result = collection.insertOne(inspection);
BsonValue id = result.getInsertedId();
System.out.println(id);
```

## insertMany()

Inserts some documents into a collection.

insertMany() accepts a list of documents and returns a InsertManyResults with the ids of the inserted documents.

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");

Document doc1 = new Document().append("account_holder","john doe").append("account_id","MDB99115881").append("balance",1785).append("account_type","checking");
Document doc2 = new Document().append("account_holder","jane doe").append("account_id","MDB79101843").append("balance",1468).append("account_type","checking");

List<Document> accounts = Arrays.asList(doc1, doc2);
InsertManyResult result = collection.insertMany(accounts);
result.getInsertedIds().forEach((x,y)-> System.out.println(y.asObjectId()));
```

### Quiz 1

Which method is available in the MongoDB Java driver to insert the following document?

```java
Document doc1 = new Document().append("account_holder","Mary Jane")
    .append("account_id","MDB123123123")
    .append("balance",2000)
    .append("account_type","savings");
```

InsertOne

Correct! We can use `insertOne` to insert a document. Here's the code:

```java
Document doc1 = new Document().append("account_holder","Mary Jane")
    .append("account_id","MDB123123123")
    .append("balance",2000)
    .append("account_type","savings");

InsertOneResult result  = collection.insertOne(doc1);
```

### Quiz 2

Which method is available in the MongoDB Java driver to insert more than one document?

InsertMany

Correct! We can use `InsertMany` to insert multile documents. Here's the syntax:
```java
collection.insertMany(documents);
```
