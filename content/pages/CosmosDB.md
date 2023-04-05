---
tags:
- CosmosDB
title: CosmosDB
categories:
date: 2022-09-21
lastMod: 2022-09-21
---


# MongoDB API

  + ## Index

    + Indexing best practices in Azure Cosmos DB's API for MongoDB are different from MongoDB. In Azure Cosmos DB's API.

  + ## Troubleshooting and Performance

    + ### Troubleshooting query performance

      + Use [`$explain`](https://learn.microsoft.com/en-us/azure/cosmos-db/mongodb/troubleshoot-query-performance#use-explain-command-to-get-metrics) command to get metrics

`mongosh
db.getCollection("my-collection").find({}).explain()
`

        + {{< logseq/orgTIP >}}The result of `$explain` in CosmosDB is not the same as MongoDB.
{{< / logseq/orgTIP >}}

        + `pathsIndexed`  shows indexes that the query used, `pathNotIndexed` shows indexes that the query could have used if available, we can check `pathNotIndexed` array and add these paths as indixes.

        + If the  `retrievedDocumentCount`  is significantly higher than the  `outputDocumentCount` , there was at least one part of your query that was unable to use an index and needed to do a scan. If the  `retrievedDocumentCount`  is approximately equal to the  `outputDocumentCount` , the query engine didn't have to scan many unnecessary documents.


