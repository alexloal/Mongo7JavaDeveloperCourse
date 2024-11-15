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

# Querying a MongoDB Collection in Java Applications

Retrieves information from a Collection.

Using `Filters` will improve these operations

## find()

Using a `Cursor` can iterate throws the results

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
try(MongoCursor<Document> cursor = collection.find(and(gte("balance", 1000),eq("account_type","checking"))).iterator()) {
    while(cursor.hasNext()) {
        System.out.println(cursor.next().toJson());
    }
}
```

## find.first()

It will retrieve the first document.

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Document doc = collection.find(Filters.and(gte("balance", 1000), Filters.eq("account_type", "checking"))).first();
System.out.println(doc.toJson());
```

# Quiz 1
Which of the following can we use to query a MongoDB database in Java? (Select all that apply.)

a. find()
Correct! We can use the find() method to query for a document in a MongoDB database with Java. This allows us to read all of the data in the database. Here's an example:
```java
collection.find(Filters.and(gte("balance", 2000)));
```

c. find().first()
Correct! We can use find().first() to query for the first document that matches a filter in a MongoDB database with Java. Here's an example:
```java
collection.find(Filters.and(lte("balance", 10000), Filters.eq("account_type","savings"))).first();
```
# Quiz 2
Which of the following Java builder class helps with querying documents by using predicates? (Select one.)

a. Filters
Correct! The Filters builder class helps us define more efficient queries by using query predicates.

# Updating Documents in Java Applications

## updateOne(`<filter>`, `<update>`) -> Update the first document found by the filter

Accepts a query filter and an update document

### Updates.push()
### Updates.pull()
### Updates.popLast()

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query  = Filters.eq("account_id","MDB12234728");
Bson updates  = Updates.combine(Updates.set("account_status","active"),Updates.inc("balance",100));
UpdateResult upResult = collection.updateOne(query, updates);
```

## updateMany(`<filter>`, `<update>`) -> Update many documents

Update multiple documents in a single operation

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query  = Filters.eq("account_type","savings");
Bson updates  = Updates.combine(Updates.set("minimum_balance",100));
UpdateResult upResult = collection.updateMany(query, updates);
```
# Quiz 1
```json line
{
   "account_id": "MDB310054629",
   "account_holder": "John Doe",
   "account_type": "savings",
   "balance": 3977.14,
   “account_status”: “active”
},
{
   "account_id": "MDB12234728",
   "account_holder": "Jane Doe",
   "account_type": "checking",
   "balance": 123.12,
   “account_status”: “dormant”
},
{
   "account_id": "MDB12234567",
   "account_holder": "Will Jackson",
   "account_type": "savings",
   "balance": 1232.12,
   “account_status”: “dormant”
}
```
Question: When you run the following update, what is the result? (Select one.)

```java
Bson query  = Filters.eq("account_id","MDB12234728");
Bson updates  = Updates.combine(Updates.set("account_status","active"),Updates.inc("balance",200));
UpdateResult upResult = collection.updateOne(query, updates);
```

b. Jane Doe has a balance of 323.12 and an active account.
Correct! The account_id matches Jane Doe's account_id, and the `updates` increase her balance by 200 and sets her account status to active.

# Quiz 2
Which of the following ways can you update document(s) in a MongoDB database? (Select all that apply.)

a. UpdateOne
Correct! We can use the `UpdateOne` method to update a document in a MongoDB database with Java. Here's the syntax:

collection.updateOne(query, update);
b. UpdateMany
Correct! We can use the `UpdateMany` method to update multiple documents in a MongoDB database with Java. Here's the syntax

collection.updateMany(query, update);

# Deleting Documents in Java Applications

## deleteOne(<filter>) -> delete a single document from a collection, returns DeleteResult object with the information of the document deleted

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query = Filters.eq("account_holder", "john doe");
DeleteResult delResult = collection.deleteOne(query);
System.out.println("Deleted a document:");
System.out.println("\t" + delResult.getDeletedCount());
```

## deleteMany(<filter>) -> delete multiple documents in a single operation, return DeleteResult object wit the information of the documents deleted

deleteMany using a empty query, it will delete all documents.

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
Bson query = eq("account_status", "dormant");
DeleteResult delResult = collection.deleteMany(query);
System.out.println(delResult.getDeletedCount());
```

```java
MongoDatabase database = mongoClient.getDatabase("bank");
MongoCollection<Document> collection = database.getCollection("accounts");
DeleteResult delResult = collection.deleteMany(Filters.eq("account_status", "dormant"));
System.out.println(delResult.getDeletedCount());
```

# Quiz 1

Use this dataset to answer the question that follows:

```java
{
   "account_id": "MDB310054629",
   "account_holder": "John Doe",
   "account_type": "savings",
   "balance": 3977.14,
   “account_status”: “active”
},
{
   "account_id": "MDB12234728",
   "account_holder": "Jane Doe",
   "account_type": "checking",
   "balance": 123.12,
   “account_status”: “dormant”
},
{
   "account_id": "MDB12234567",
   "account_holder": "Will Jackson",
   "account_type": "savings",
   "balance": 1232.12,
   “account_status”: “dormant”
}
```

Question: When you run the following update, what is the result? (Select one.)

```java
Bson query = Filters.eq("account_holder", "Will Jackson");
DeleteResult delResult = collection.deleteOne(query);
```

Correct Answer

John Doe and Jane Doe are the only two documents left in the collection.
Correct! The filter matches `account_holder` and `Will Jackson` and deletes that document from the collection.

# Quiz 2

Which of the following ways can you delete document(s) MongoDB database in Java? (Select all that apply.)

Correct Answer

b. DeleteOne
Correct! We can use the `DeleteOne` method to delete a document in a MongoDB database with Java. Here's the syntax:

`collection.deleteOne(query);`

c. DeleteMany
Correct! We can use the `DeleteMany` method to delete multiple documents in a MongoDB database with Java. Here's the syntax:

`collection.deleteMany(query);`

# Creating MongoDB Transactions in Java Applications

## Transactions 
- A multi-document transaction is an operation that requires atomicity of reads and/or writes to multiple documents.
- A transaction is a sequence of database operations that represent a single unit of work.
- If the transaction is canceled or doesn't complete, all write operations performed are discarded.

## ACID
- Atomicity
- Consistency
- Isolation
- Durability

## Transaction steps:
1. Start a client session
2. Define the transaction options (optional)
3. Define the sequence of operations to perform inside the transactions
4. Start the transactions by using the ClientSession's `withTransaction()` method
5. Release the resources used by the transaction

### Remember:
- There's a 60-second time limit
- Close any resources the transaction may use

```java
final MongoClient client = MongoClients.create(connectionString);
final ClientSession clientSession = client.startSession();

TransactionBody txnBody = new TransactionBody<String>(){
    public String execute() {
        MongoCollection<Document> bankingCollection = client.getDatabase("bank").getCollection("accounts");

        Bson fromAccount = eq("account_id", "MDB310054629");
        Bson withdrawal = Updates.inc("balance", -200);

        Bson toAccount = eq("account_id", "MDB643731035");
        Bson deposit = Updates.inc("balance", 200);

        System.out.println("This is from Account " + fromAccount.toBsonDocument().toJson() + " withdrawn " + withdrawal.toBsonDocument().toJson());
        System.out.println("This is to Account " + toAccount.toBsonDocument().toJson() + " deposited " + deposit.toBsonDocument().toJson());
        bankingCollection.updateOne(clientSession, fromAccount, withdrawal);
        bankingCollection.updateOne(clientSession, toAccount, deposit);

        return "Transferred funds from John Doe to Mary Doe";
    }
};

try {
    clientSession.withTransaction(txnBody);
} catch (RuntimeException e){
    System.out.println(e);
}finally{
    clientSession.close();
}
```

## Quiz 1
Transactions in MongoDB follow ACID principles.

True

## Quiz 2
Use this code to answer the question that follows:

```java
public static void main(String[] args) {
   String connectionString = System.getProperty("mongodb.uri");

   final MongoClient client = MongoClients.create(connectionString);
   final ClientSession clientSession = client.startSession();

   TransactionBody txnBody = new TransactionBody<String>(){
       public String execute() {
       }
   };
   try {
       clientSession.withTransaction(txnBody);
   } catch (RuntimeException e){
       System.out.println(e);
   }finally{
       clientSession.close();
   }
```
Question: In this code, where should we call the CRUD operations to complete the transaction? (Select one.)

Inside the `public String execute()` method
Correct! We need to call the CRUD operations inside the `public String execute()` method.