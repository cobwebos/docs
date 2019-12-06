---
title: Azure Cosmos DB 中的参数化查询
description: 了解 SQL 参数化查询如何为用户输入提供可靠的处理和转义，并通过 SQL 注入防止数据意外泄露。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870813"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB 中的参数化查询

Cosmos DB 支持使用熟悉 @ 表示法表示的参数的查询。 参数化 SQL 为用户输入提供可靠的处理和转义，并可防止通过 SQL 注入发生意外的数据泄露。

## <a name="examples"></a>示例

例如，你可以编写一个查询，该查询采用 `lastName` 和 `address.state` 作为参数，并根据用户输入对 `lastName` 和 `address.state` 的各个值执行该查询。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然后，你可以将此请求作为参数化 JSON 查询发送到 Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

下面的示例使用参数化查询设置 TOP 参数： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

参数值可以是任何有效的 JSON：字符串、数字、布尔值、null，甚至是数组或嵌套的 JSON。 由于 Cosmos DB 无架构，因此不会针对任何类型对参数进行验证。


## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文档数据](modeling-data.md)
