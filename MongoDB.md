In MongoDB, the hierarchy of data storage is as follows:

## Database:
 This is the highest level of organization in MongoDB. A single MongoDB server can host multiple databases.
## Collection:
 Within a database, data is stored in collections. A collection is analogous to a table in relational databases but does not enforce a schema.
## Document:
 The individual records within a collection are called documents. Documents are stored in a format called BSON (Binary JSON), which allows for a flexible schema and supports various data types.

So, the correct hierarchy is:
Database > Collection > Document

The maximum BSON document size in MongoDB is 16 megabytes.
## Datatypes
* strings
* int
* float
* boolean
* date
* null
* arrays
* nested docs or Objects

**There are also different operators which are used in mongodb. They are started with "$"**

# Some Commands
1. **show dbs**: For showing all existing databases.

2. **use [db name]**: It will use the database or if the db not exists it will create one.

3. **db.createCollection("students")**: It will create a collection named "students" in the database after 'using'.

4. **db.dropDatabase()**: It will drop the database

5. **show collections**: To show all the collections in a database

6. **db.students.insertOne({name: "suvam", age:"21", city: "bbsr"})**: To insert a document in a collection. If the collection "students" not exist, it will create one.

7. **db.students.find()**: To get the documents in a collections. meanwhile findOne() return only one or first document

8. **db.students.insertMany([ {...} , {...} , {...} ])**: To insert many documents at a time

9. **db.students.find().sort({name:1})**: To sorting by the name in ascending order (1) / descending order(-1).

10. **db.students.find().limit(1)**:  To limit the return we got as output

11. **db.students.find({age:"21"})**: To return the documents where age="21"

12. **db.students.find({age:"21"}, {name:true})**: To return the name in the documents where age="21"

13. **db.students.find({age:"21"}, {_id:false, name:true})**: To return the name and exclude _id in the documents where age="21"

14. **db.students.updateOne({name:"kamal"},{$set: {outsider:false}})**: To set outsider=false (if outsider not exists it will create one) where name="kamal", we can also use update(), replaceOne()
Note that the $set operator is typically used to modify specific fields within a document without replacing the entire document.

15. **db.students.updateOne({name:"kamal"},{$unset: {outsider:""}})**: To unset or remove a particular field.

16. **db.students.updateMany({outsider:{$exists:false}},{$set:{outsider:false}})**: same but here with many documents. in this example the fields will be updated to outsider=false where outsider don't exists

17. **db.students.deleteOne({...}) /deleteMany({...},{...})**: for deleting any document

18. **db.students.find({name:{$ne:"kamal"}})**: $ne stands for not equal to.

19. similar to above there are $lt (less than), $lte (less than equal to) and $gt, $gte as well.
**example: db.students.find({age:{$lt:23, $gt:20}}): to find the students whose age > 20 and < 23
// { $gte : "A" , $lte : "C" } specifies that we want to find documents where the "actor" field is greater than or equal to "A" and less than or equal to "C". MongoDB does support string comparisons using $gte, $lte, $gt, and $lt.

20. **db.students.find({name:{$in:["neha", "shivom", "suvam"]}})**: To find out the students whose name are in given array. also there is "not in" ($nin)

21. **db.students.find({$and: [{city:"cuttack"},{age:{$gt:"20"}}]})**: To find out the students whose city=cuttack and age > 20. similar to this "$or", "$nor", "$ne" (not equal to)

22. **db.students.drop()**: To drop a collection

23. **db.students.replaceOne({name:"shivom"},{name:"shivom samal"})**: To replace the document with the given one where name=shivom

24. 
```javascript
db.books.updateMany(
    { "BookPublisher": "Justin Paul" },
    {
        $inc: { "BookPrice": 200 },
        $set: { "BookCopies": 10000 },       
        $push: { "BookLanguage": "Tamil" }
    }
)
```
To increase 200 by existing BookPrice value, update BookCopies by 10000, Push another element by "Tamil"

25. **db.books.find({"BookCategory":{$size:2}}).count()**: To count the no. of books having only two book categories

26. suppose we have a embeded document as a field like 
```javascript
{
 _id:12, 
 topics:{ 
     start: "may",
     end: "june" 
     }
}   
```                                                     
here we can access the insiders by **{topics.start:....., topic.end: .....}**

 ### After the basic CRUD operations, there comes the advanced aggregation methods. Aggregation operations process multiple documents and return computed results. To perform aggregation operations, you can use: 

 ## 1. aggregation pipeline
An aggregation pipeline consists of one or more stages that process documents:

* Each stage performs an operation on the input documents. For example, a stage can filter documents, group documents, and calculate values.

* The documents that are output from a stage are passed to the next stage.

* An aggregation pipeline can return results for groups of documents. For example, return the total, average, maximum, and minimum values.

Aggregation pipelines run with the db.collection.aggregate() method do not modify documents in a collection, unless the pipeline contains a $merge or $out stage.
```javascript
db.orders.aggregate( [

   // Stage 1: Filter pizza order documents by pizza size
   {
      $match: { size: "medium" }
   },

   // Stage 2: Group remaining documents by pizza name and calculate total quantity
   {
      $group: { _id: "$name", totalQuantity: { $sum: "$quantity" } }
   }

] )
```
**The $match stage:**

* Filters the pizza order documents to pizzas with a size of medium.

* Passes the remaining documents to the $group stage.

**The $group stage**:

* Groups the remaining documents by pizza name.

* Uses $sum to calculate the total order quantity for each pizza name. The total is stored in the totalQuantity field returned by the aggregation pipeline.

 ## 2. single purpose aggregation methods
 The single purpose aggregation methods aggregate documents from a single collection. The methods are simple but lack the capabilities of an aggregation pipeline.
 example: 
 ```javascript
 db.collection.estimatedDocumentCount()
//Returns an approximate count of the documents in a collection or a view.

db.colleciton.count()
//Returns a count of the number of documents in a collection or a view.

db.collection.distinct()
//Returns an array of documents that have distinct/unique values for the specified field.

 ```
 ## 3. Map-reduce
 **Starting in MongoDB 5.0, map-reduce is deprecated.**
 ![Demo](/images/map-reduce.bakedsvg.svg)



*In aggregation pipeline, There are few important operators like*
#### $match
The $match operator in MongoDB is used to filter documents based on specified criteria. It allows you to select only those documents that meet certain conditions.
```javascript
db.products.aggregate([
  {
    $match: {
      price: { $gt: 150 }
    }
  }
]);
```

#### $unwind
The $unwind stage in MongoDB is used to deconstruct an array field into multiple documents, one for each element of the array. This is particularly useful when you need to perform operations on each element of an array individually.
```javascript
//input:
{
  "_id": 1,
  "name": "Alice",
  "addresses": [
    { "city": "New York", "state": "NY" },
    { "city": "Los Angeles", "state": "CA" }
  ]
}
{
  "_id": 2,
  "name": "Bob",
  "addresses": [
    { "city": "Chicago", "state": "IL" }
  ]
}

//query:
db.customers.aggregate([
  {
    $unwind: "$addresses"
  }
])

//output:
{ "_id": 1, "name": "Alice", "addresses": { "city": "New York", "state": "NY" } }
{ "_id": 1, "name": "Alice", "addresses": { "city": "Los Angeles", "state": "CA" } }
{ "_id": 2, "name": "Bob", "addresses": { "city": "Chicago", "state": "IL" } }
```
#### $group
The $group stage in MongoDB is used to group documents by one or more fields and perform calculations on the grouped data. It's a powerful tool for aggregating data and generating summary statistics.

```javascript
//input
{ "_id": 1, "customer": "Alice", "total": 100 }
{ "_id": 2, "customer": "Bob", "total": 200 }
{ "_id": 3, "customer": "Alice", "total": 50 }

//query
db.orders.aggregate([
  {
    $group: {
      _id: "$customer",
      totalSpent: { $sum: "$total" }
    }
  }
]);

//output
{ "_id": "Alice", "totalSpent": 150 }
{ "_id": "Bob", "totalSpent": 200 }
```

there are different types of accumulators like $sum:
* $avg
* $first
* $last
* $max
* $min
* $addToSet
* $push
* $stdDevPop
* $stdDevSamp


#### $project
The $project stage in MongoDB is used to select specific fields from documents and optionally create new fields or modify existing ones. It's a versatile tool for transforming the structure of your data.

#### $skip
#### $sort
#### $limit
#### $lookup
The $lookup stage in MongoDB is used to join data from two different collections based on a specified relationship. It's a powerful tool for relational-like operations in a NoSQL database.

```javascript
//template
{
  $lookup: {
    from: <collection name>,
    localField: <local field>,
    foreignField: <foreign field>,
    as: <output array field name>
  }
}
```
* from: The name of the collection to join.
* localField: The field in the input documents to use for the join.
* foreignField: The field in the from collection to use for the join.
* as: The name of the output array field that will contain the matched documents from the from collection.

**Example**: Consider two collections:

orders: Contains orders with a customer_id field.
customers: Contains customer information with a _id field.
To join the orders collection with the customers collection based on the customer_id field, you can use the following aggregation pipeline:
```javascript
db.orders.aggregate([
  {
    $lookup: {
      from: "customers",
      localField: "customer_id",
      foreignField: "_id",
      as: "customer"
    }
  }
]);
```
**This will produce documents that include the original order information and a new customer array field containing the matching customer document.**

there are also different operators like:
* $add
* $divide
* $multiply
* $all
* $elemMatch
* $size
* $arrayElemAt
* $concatArrays
* $regex
* $inc
* $set
* $unset
* $addToSet
* $pop
* $pull
* $push
* $and
* $not
* $or
* $cond
* $ifNull
* $switch
* $dayOfMonth
* $dayOfWeek
* $hour
* $month
* $minute
* $year
* $concat
* $substr
* $toLower


```javascript
db.books.aggregate([
    { $match: { BookCategory: "Classics" } },
    { $project: { BookTitle: 1, BookAuthor: 1, BookPrice: 1, PriceList: { $cond: [
        { $gt: ['$BookPrice', 10000] },
        "High Cost",
        { $cond: [
            { $and: [{ $gte: ['$BookPrice', 5000] }, { $lt: ['$BookPrice', 10000] }] },
            "Medium Cost",
            "Least Cost"
        ]}
    ]} }}
]);
```
