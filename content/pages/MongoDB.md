---
tags:
- MongoDB
title: MongoDB
categories:
date: 2022-08-22
lastMod: 2022-08-26
---


## Document


  + MongoDB stores data records as [BSON](http://bsonspec.org/) documents.

  + ### Document Structure

    + MongoDB documents are composed of field-and-value pairs and have the following structure. The [field name](https://www.mongodb.com/docs/manual/core/document/#field-names)s are strings, and the value of a field can be any of the BSON [data types](https://www.mongodb.com/docs/manual/reference/bson-types/).

`json
{
   field1: value1,
   field2: value2,
   field3: value3,
   ...
   fieldN: valueN
}
`

    + 

## Indexes


  + [Indexes](https://www.mongodb.com/docs/manual/indexes/) is the key for efficient read queries. Without indexes, MongoDB must perform a *collection scan (scan every document in a collection)* to find those documents that match the query statement.

  + MongoDB defines indexes at the [collection](https://www.mongodb.com/docs/manual/reference/glossary/#std-term-collection) level and supports indexes on any field or sub-field of the documents in a MongoDB collection. The index stores the value of a specific field or set of fields, ordered by the value of the field.

  + {{< logseq/orgCAUTION >}}Applications may encounter reduced performance during index builds, including limited read/write access to the collection.
{{< / logseq/orgCAUTION >}}

  + ### Default `_id` index


    + MongoDB creates a [unique index](https://www.mongodb.com/docs/manual/core/index-unique/#std-label-index-type-unique) on the [_id](https://www.mongodb.com/docs/manual/core/document/#std-label-document-id-field) field during the creation of a collection. The  `_id`  index prevents clients from inserting two documents with the same value for the  `_id`  field.

  + ### Index Types


    + **Single Field Index**: indexes on a single field of a document, the default `_id` index is single field index.

    + **Compound Index**: indexes on multiple fields.

    + **Multikey Index**: MongoDB uses multikey indexes to index the content stored in arrays. If you index a field that holds an array value, MongoDB creates separate index entries for *every* element of the array.

    + **Geospatial Index**: used to support efficient queries of geospatial coordinate data, including [2d indexes](https://www.mongodb.com/docs/manual/core/2d/) that uses planar geometry when returning results and [2dsphere indexes](https://www.mongodb.com/docs/manual/core/2dsphere/) that use spherical geometry to return results.

    + **Text index**: support text search queries on string content in a collection.

    + **Hashed index**: for the support of hash based sharding, hashed index indexes the has of the value of a field. This index type only support equality matches and cannot support range-based queries.

  + ### Covered Queries


    + When the query criteria and the [projection](https://www.mongodb.com/docs/manual/reference/glossary/#std-term-projection) of a query include *only* the indexed fields, MongoDB returns results directly from the index *without* scanning any documents or bringing documents into memory.

    + An index [covers](https://www.mongodb.com/docs/manual/core/query-optimization/#std-label-indexes-covered-queries) a query when all of the following apply:

      + all the fields in the [query](https://www.mongodb.com/docs/manual/tutorial/query-documents/#std-label-read-operations-query-document) are part of an index, **and**

      + all the fields returned in the results are in the same index.

      + no fields in the query are equal to  `null`  (i.e. { `"field" : null` } or { `"field" : {$eq : null}}`  ).

  + 

## MongoDB Java Drivers


  + Use [Java Driver](https://www.mongodb.com/docs/drivers/java/sync/current) for synchronous Java applications, use [Reactive Streams Driver](https://www.mongodb.com/docs/drivers/reactive-streams/) to use the Reactive Streams API for asynchronous stream processing.

## Performance Concerns


  + Pagination


    + Pagination using `skip()` is slow, but we can use `_id` and `limit()` as work around.

      + `skip()` doesn't use indexes to jump to the right place because indexes are stored as trees rather than arrays.

    + [Fast Paging with MongoDB](https://scalegrid.io/blog/fast-paging-with-mongodb/).

  + Optimize query performance


    + Read [more](https://www.mongodb.com/docs/manual/tutorial/optimize-query-performance-with-indexes-and-projections/).

    + Create indexes to support queries

    + Limit the number of query results to reduce network demand

    + Use projections to return only necessary data

    + Use `$hint` to select a particular index

    + Use `$inc` to perform increment operation at server-side

  + Optimize write performance

    + Each index on a collection adds some amount of overhead to the performance of write operations.

  + Worth to read

    + [MongoDB Performance Tunning](https://medium.com/mongodb-performance-tuning).

    + [Why shouldn’t I embed large arrays in my documents](https://www.percona.com/blog/2014/02/17/dont-worry-about-embedding-large-arrays-in-your-mongodb-documents/).

    + [Performance Best Practices: Query Patterns and Profiling](https://www.mongodb.com/blog/post/performance-best-practices-query-patterns-and-profiling)

    + [Performance Best Practices: Indexing](https://www.mongodb.com/blog/post/performance-best-practices-indexing).

## Tools

  + [MongoDB Shell (mongosh)]({{< ref "/pages/MongoDB Shell (mongosh)" >}})
