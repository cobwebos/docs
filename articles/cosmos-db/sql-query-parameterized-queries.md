---
title: Azure Cosmos DB 中的参数化查询
description: 了解 SQL 参数化查询如何提供对用户输入的可靠处理和转义，并通过 SQL 注入防止意外泄露数据。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: f66bc89ef56dd0c2291903d531a4637210abd8df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87496978"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB 中的参数化查询

Azure Cosmos DB 支持使用带有常用 @ 表示法的参数进行查询。 参数化 SQL 为用户输入提供可靠的处理和转义，可防止通过 SQL 注入发生意外的数据泄露。

## <a name="examples"></a>示例

例如，可以编写一个将 `lastName` 和 `address.state` 用作参数的查询，并根据用户输入针对 `lastName` 和 `address.state` 的各种值执行此查询。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然后，可将此请求作为参数化 JSON 查询发送到 Azure Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

以下示例使用参数化查询设置 TOP 参数：

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

参数值可以是任何有效的 JSON：字符串、数字、布尔值、null，甚至数组或嵌套的 JSON。 由于 Azure Cosmos DB 是无架构的，因此不会针对任何类型验证参数。

下面是每个 Azure Cosmos DB SDK 中参数化查询的示例：

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文档数据](modeling-data.md)
