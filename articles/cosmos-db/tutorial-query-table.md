---
title: 如何查询 Azure Cosmos DB 中的表数据？
description: 了解如何使用 OData 筛选器和 LINQ 查询来查询 Azure Cosmos DB 表 API 帐户中存储的数据
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 06/05/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 481f1353e16fecd0e413152db89a4ae54824a0f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019277"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>教程：使用表 API 查询 Azure Cosmos DB

Azure Cosmos DB [表 API](table-introduction.md) 支持针对键/值（表）数据进行 OData 和 [LINQ](/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) 查询。  

本文涵盖以下任务：

> [!div class="checklist"]
> * 使用表 API 查询数据

本文中的查询使用如下示例 `People` 表：

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 |

有关如何使用表 API 执行查询的详细信息，请参阅[查询表和实体](/rest/api/storageservices/fileservices/querying-tables-and-entities)。

有关 Azure Cosmos DB 所提供的高级功能的详细信息，请参阅 [Azure Cosmos DB 表 API](table-introduction.md) 和[使用 .NET 通过表 API 进行开发](tutorial-develop-table-dotnet.md)。

## <a name="prerequisites"></a>先决条件

若要使这些查询生效，必须拥有 Azure Cosmos DB 帐户，且容器中必须包含实体数据。 没有这些内容？ 请学习[五分钟快速入门](create-table-dotnet.md)或者[开发人员教程](tutorial-develop-table-dotnet.md)，创建帐户并填充数据库。

## <a name="query-on-partitionkey-and-rowkey"></a>根据 PartitionKey 和 RowKey 查询

由于 PartitionKey 和 RowKey 属性构成实体的主键，因此可以使用以下特殊语法来识别实体：

**查询**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```

**结果**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

或者，可将这些属性指定为 `$filter` 选项的一部分，如以下部分中所示。 请注意，键属性名称和常量值区分大小写。 PartitionKey 和 RowKey 属性皆为 String 类型。

## <a name="query-by-using-an-odata-filter"></a>使用 OData 筛选器进行查询

构造筛选器字符串时，请记住以下规则：

* 使用由 OData 协议规范定义的逻辑运算符比较属性和值。 请注意，不能将属性与动态值进行比较。 表达式的一侧必须是常量。
* 必须使用 URL 编码空格分隔属性名称、运算符和常量值。 空格经 URL 编码后为 `%20`。
* 筛选器字符串的所有部分都区分大小写。
* 常量值的数据类型必须与属性的类型相同，这样筛选器才能返回有效的结果。 有关支持的属性类型的详细信息，请参阅 [Understanding the Table Service Data Model](/rest/api/storageservices/understanding-the-table-service-data-model)（了解表服务数据模型）。

以下示例查询演示如何使用 OData `$filter` 按 PartitionKey 和 Email 属性进行筛选。

**查询**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

有关如何对不同数据类型构造筛选表达式的详细信息，请参阅 [Querying Tables and Entities](/rest/api/storageservices/querying-tables-and-entities)（查询表和实体）。

**结果**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Smith |Ben | Ben@contoso.com| 425-555-0102 |

在 Azure Cosmos DB 的表 API 中执行查询时，对 datetime 属性的查询不返回任何数据。 虽然 Azure 表存储使用时钟周期的时间粒度存储日期值，但 Azure Cosmos DB 中的表 API 使用 `_ts` 属性。 `_ts` 属性处于第二个粒度级别，它不是 OData 筛选器。 因此，Azure Cosmos DB 阻止了有关 timestamp 属性的查询。 解决方法是，可以定义自定义日期/时间或长数据类型属性，并设置客户端的日期值。

## <a name="query-by-using-linq"></a>使用 LINQ 进行查询 
还可使用 LINQ 进行查询，这会转换为相应的 OData 查询表达式。 以下示例演示如何使用 .NET SDK 生成查询。

```csharp
IQueryable<CustomerEntity> linqQuery = table.CreateQuery<CustomerEntity>()
            .Where(x => x.PartitionKey == "4")
            .Select(x => new CustomerEntity() { PartitionKey = x.PartitionKey, RowKey = x.RowKey, Email = x.Email });
```

## <a name="next-steps"></a>后续步骤

在本教程中已完成以下操作：

> [!div class="checklist"]
> * 已了解如何使用表 API 进行查询

现在可继续学习下一教程，了解如何全局发布数据。

> [!div class="nextstepaction"]
> [全局分发数据](tutorial-global-distribution-table.md)
