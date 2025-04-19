
# All about MongoDB command 📝  

## SQL to MongoDB Mapping Chart

This document provides a mapping of common SQL terminology and concepts to their MongoDB equivalents

---

### 📘 Terminology and Concepts

The following table maps SQL terms to their MongoDB counterparts:

| SQL Terms/Concepts        | MongoDB Terms/Concepts                                 |
|--------------------------|--------------------------------------------------------|
| **database**             | database                                               |
| **table**                | collection                                             |
| **row**                  | document or BSON document                              |
| **column**               | field                                                  |
| **index**                | index                                                  |
| **table joins**          | `$lookup`, embedded documents                          |
| **primary key**          | Specify any unique column or column combination        |
| **primary key (MongoDB)**| `_id` field is the default primary key                 |
| **aggregation (e.g. GROUP BY)** | aggregation pipeline                          |
| **SELECT INTO NEW_TABLE**| `$out` (see Aggregation Mapping Chart)                 |
| **MERGE INTO TABLE**     | `$merge` (see Aggregation Mapping Chart)               |
| **UNION ALL**            | `$unionWith`                                           |
| **transactions**         | transactions                                           |

> 💡 For many scenarios, MongoDB's denormalized data model (using embedded documents and arrays) is optimal. This often reduces the need for multi-document transactions.

---


## Installation
### With Docker
#### Pull the MongoDB Docker Image
    docker pull mongodb/mongodb-community-server:latest

#### Run the Image as a Container
    docker run --name mongodb -p 27017:27017 -d mongodb/mongodb-community-server:latest

#### Go to container and Connect to the MongoDB Deployment with mongosh
    mongosh --port 27017

for more detail [click here](https://www.mongodb.com/docs/manual/installation/)



## Databases Crud Commands  🚀  
### List Databases
<p> below commands use to list all databses in mongoDB <p>  

    show dbs;

or 

    show databases;
### To select current DB/Create Database
Below command is used to creating a database.
This command will tell the MongoDB client to create a database by this name if there is no database exists by this name. Otherwise, this command will return the existing database.

```
use <db_name>;
```  
### Update Databases
    db.copyDatabase("oldDBName", "newDBName")
### Delete Databases
current selected databases will be deleted
 ```
 db.dropDatabase()
 ```

### Dump data to files(Export database)
```
mongodump --out c:\backupDatabase
```
### Load load data from files(Import database)
```
mongorestore --db databaseName --collection <collectionName>   <directory\collectionName.bson>
```
<br>

## Collections(Table) CRUD Commands  🚀  
### List All Collections
```
show collections;
```
or

```
show tables;
```
or
```
db.getCollectionNames();
```

### Create Collections
```
 db.createCollection("<Collection_name>");
```
### Update Collections(Rename)
```
 db.<collection_name>.renameCollection("<new_collection_name>")

```
### Delete Collections

```
db.<collection_name>.drop()
```

## Documents(Rows) CRUD Commands
### Insert Documents

Insert single Documents
```
db.<collection_name>.insertOne(
  {
    title: "The Favourite",
    genres: [ "Drama", "History" ],
    runtime: 121,
    rated: "R",
    year: 2018,
    directors: [ "Yorgos Lanthimos" ],
    cast: [ "Olivia Colman", "Emma Stone", "Rachel Weisz" ],
    type: "movie"
  }
)
```
Insert multiple Documents

```

db.<collection_name>.insertMany([
   { title: "Jurassic World: Fallen Kingdom", genres: [ "Action", "Sci-Fi" ], runtime: 130, rated: "PG-13", year: 2018, directors: [ "J. A. Bayona" ]},
   { title: "Tag",                            genres: [ "Comedy", "Action" ], runtime: 105, rated: "R",     year: 2018, directors: [ "Jeff Tomsic" ]}
])
```

### Fetch Documents(rows)
To fetch all data from collection

```
db.<collection_name>.find({})
```
To fetch all document where key(column name )=value

```
db.<collection_name>.find( { "title": "Titanic" } )
```
<b>$In</b> Operator is used to fetch all document  where key in []

```
db.<collection_name>.find( { rated: { $in: [ "PG", "PG-13" ] } } )
```
meaning: <i>select * from collection_name where rated in ("PG", "PG-13")</i>


Specify Logical Operators (AND)

`AND`
```
db.movies.find( { countries: "Mexico", "imdb.rating": { $gte: 7 } } )
```
meaning <i>SELECT *
FROM movies
WHERE countries = 'Mexico' AND imdb_rating >= 7;
</i>
 
 `$OR`  Operator 

```
db.movies.find( {
     year: 2010,
     $or: [ { "awards.wins": { $gte: 5 } }, { genres: "Drama" } ]
} )
```
meaning <i> 
SELECT m.*
FROM movies m
LEFT JOIN movie_genres mg ON m.id = mg.movie_id
WHERE m.year = 2010
  AND (
    m.awards_wins >= 5 OR mg.genre = 'Drama');
</i>





### Update Documents
 To update single document `updateOne` method
```
db.movies.updateOne({title:{"$regex":"the$"}},{ $set: {"awards.win":12}, $currentDate : {lastupdated:true}})
```
meaning <i>
UPDATE movies
SET awards_win = 12,
    lastupdated = CURRENT_TIMESTAMP
WHERE id = (
  SELECT id
  FROM movies
  WHERE title ~* 'the$'
  LIMIT 1
);
</i>

To update multiple Documents `updateMany` method
```
db.movies.updateMany({title:{"$regex":"the$"}},{ $set: {"awards.win":12,year:2015}, $currentDate : {lastupdated:true}})
```
meaning <i>
UPDATE movies
SET awards_win = 12,
    year = 2015,
    lastupdated = CURRENT_TIMESTAMP
WHERE title ~* 'the$'; </i>


To replace the entire content of a document except for the _id field, pass an entirely new document as the second argument to `db.collection.replaceOne()`
```
db.<collection_name>.replaceOne(
  { account_id: 371138 },
  { account_id: 893421, limit: 5000, products: [ "Investment", "Brokerage" ] }
)
```
meaning : above command will find one document which have account_id=371138 and set new document Specify as second argument




### Delete Documents
To delete one document use `deleteOne` method
```
 db.movies.deleteOne({title: 'Breathe'})
```

To delete multiple document use `deleteMany` method
```
 db.movies.deleteMany({"imdb.rating":{$gt:1, $lt: 4}})
```


## Sorting Documents
desceding order 
```
 db.<collection_name>.find().sort({<key_name>>:-1})
```
asceding order 
```
 db.<collection_name>.find().sort({<key_name>>:1})
```
## limit and Skip
using `limit` keyword we can specify number of document for output.
```
 db.movies.find().limit(1)
```
using `skip` keyword we can skip first given number of document for output. query will skip given number of document and return documents after skipped document
```
 db.movies.find().skip(1)
```



# MongoDB Query and Projection Operators

This guide provides a detailed overview of MongoDB Query Selectors, Logical Operators, Evaluation, Geospatial Queries, Array Queries, Bitwise Operations, Projection Operators, and Miscellaneous Operators.

---

## 📗 Query Selectors

### Comparison Operators

| Name  | Description |
|-------|-------------|
| `$eq` | Matches values that are equal to a specified value. |
| `$gt` | Matches values that are greater than a specified value. |
| `$gte` | Matches values that are greater than or equal to a specified value. |
| `$in` | Matches any of the values specified in an array. |
| `$lt` | Matches values that are less than a specified value. |
| `$lte` | Matches values that are less than or equal to a specified value. |
| `$ne` | Matches all values that are not equal to a specified value. |
| `$nin` | Matches none of the values specified in an array. |

---

## 📘 Logical Operators

| Name  | Description |
|-------|-------------|
| `$and` | Joins query clauses with a logical **AND**; returns documents matching all conditions. |
| `$not` | Inverts the effect of a query predicate; returns documents that do not match the predicate. |
| `$nor` | Joins query clauses with a logical **NOR**; returns documents that fail to match both clauses. |
| `$or` | Joins query clauses with a logical **OR**; returns documents matching either clause. |

---

## 📙 Element Operators

| Name  | Description |
|-------|-------------|
| `$exists` | Matches documents that contain the specified field. |
| `$type` | Selects documents if a field is of the specified BSON type. |

---

## 📒 Evaluation Operators

| Name  | Description |
|-------|-------------|
| `$expr` | Allows the use of aggregation expressions within the query language. |
| `$jsonSchema` | Validates documents against the given JSON Schema. |
| `$mod` | Performs a modulo operation on the value of a field and selects documents with the specified result. |
| `$regex` | Selects documents where values match a specified regular expression. |
| `$text` | Performs text search. *(For Atlas, use Atlas Search for improved capabilities.)* |
| `$where` | Matches documents that satisfy a JavaScript expression. |

---

## 🗺️ Geospatial Operators

| Name  | Description |
|-------|-------------|
| `$geoIntersects` | Selects geometries that intersect with a GeoJSON geometry (supports `2dsphere` index). |
| `$geoWithin` | Selects geometries within a bounding GeoJSON geometry (supports `2dsphere` and `2d` indexes). |
| `$near` | Returns geospatial objects near a point (requires a geospatial index). |
| `$nearSphere` | Returns geospatial objects near a point on a sphere (requires a geospatial index). |

---

## 📚 Array Operators

| Name  | Description |
|-------|-------------|
| `$all` | Matches arrays that contain all elements specified in the query. |
| `$elemMatch` | Selects documents if an element in the array field matches all specified conditions. |
| `$size` | Selects documents if the array field is a specified size. |

---

## 🧠 Bitwise Operators

| Name  | Description |
|-------|-------------|
| `$bitsAllClear` | Matches values where all specified bit positions are clear (0). |
| `$bitsAllSet` | Matches values where all specified bit positions are set (1). |
| `$bitsAnyClear` | Matches values where any specified bit position is clear (0). |
| `$bitsAnySet` | Matches values where any specified bit position is set (1). |

---

## 🖼️ Projection Operators

| Name  | Description |
|-------|-------------|
| `$` | Projects the first array element that matches the query condition. |
| `$elemMatch` | Projects the first array element that matches the specified `$elemMatch` condition. |
| `$meta` | Projects the document's score assigned during the `$text` operation. |
| `$slice` | Limits the number of elements projected from an array (supports skip and limit). |

---

## 🔀 Miscellaneous Operators

| Name  | Description |
|-------|-------------|
| `$rand` | Generates a random float between 0 and 1. |
| `$natural` | A special hint to force forward or reverse collection scan (`sort()` or `hint()` methods). |

---




