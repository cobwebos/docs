---
title: 在 Azure Cosmos DB 中使用 JavaScript 查询 API 编写存储过程和触发器
description: 了解如何在 Azure Cosmos DB 中使用 JavaScript 查询 API 编写存储过程和触发器
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.openlocfilehash: bc92a1276232506d291e367f74a9dcf8d58d7e07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982320"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>如何使用 JavaScript 查询 API 在 Azure Cosmos DB 中编写存储过程和触发器

Azure Cosmos DB 允许使用流畅的 JavaScript 接口执行优化的查询，不需要了解可以用来编写存储过程或触发器的 SQL 语言。 若要详细了解 Azure Cosmos DB 对 JavaScript 查询 API 的支持，请参阅[在 Azure Cosmos DB 中使用 JavaScript 语言集成的查询 API](javascript-query-api.md) 一文。

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>使用 JavaScript 查询 API 的存储过程

下面的代码示例是一个有关在存储过程的上下文中使用 JavaScript 查询 API 的示例。 此存储过程插入由输入参数指定的 Azure Cosmos 项，并使用 `__.filter()` 方法更新元数据文档，其中 minSize、maxSize 和 totalSize 基于输入项的 size 属性。

> [!NOTE]
> 使用 JavaScript 查询 API 时，`__`（双下划线）是 `getContext().getCollection()` 的别名。

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>后续步骤

参阅以下文章来了解 Azure Cosmos DB 中的存储过程、触发器和用户定义的函数：

* [如何在 Azure Cosmos DB 中使用存储过程、触发器和用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md)

* [如何在 Azure Cosmos DB 中注册和使用存储过程](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* 如何在 Azure Cosmos DB 中注册和使用[事前触发器](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)和[事后触发器](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)

* [如何在 Azure Cosmos DB 中注册和使用用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Azure Cosmos DB 中的综合分区键](synthetic-partition-keys.md)
