---
title: "Azure Cosmos DB 中的唯一键 | Microsoft 文档"
description: "了解如何使用 Azure Cosmos DB 数据库中的唯一键。"
services: cosmos-db
keywords: "唯一键约束, 违反唯一键约束"
author: rafats
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b15d5041-22dd-491e-a8d5-a3d18fa6517d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: rafats
ms.openlocfilehash: c530b34edf9bfa0651b7b114dcf7e8add0d906ed
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB 中的唯一键

开发人员可以使用唯一键对其数据库添加一层数据完整性。 创建容器时，通过创建唯一键策略，可确保每个[分区键](partition-data.md)的一个或多个值的唯一性。 使用唯一键策略创建容器后，它可以避免创建值与唯一键约束指定的值重复的任何新的或更新项。   

> [!NOTE]
> 最新版本的 [.NET](sql-api-sdk-dotnet.md) 和 [.NET Core](sql-api-sdk-dotnet-core.md) SQL SDK 以及 [MongoDB API](mongodb-feature-support.md#unique-indexes) 支持唯一键。 表 API 和图形 API 目前不支持唯一键。 
> 
>

## <a name="use-case"></a>使用案例

例如，我们来看一看与[社交应用程序](use-cases.md#web-and-mobile-applications)关联的用户数据库如何从对电子邮件地址实施唯一键策略中受益。 通过使用户的电子邮件地址成为唯一键，确保每条记录具有唯一的电子邮件地址，并且不会创建具有重复电子邮件地址的新记录。 

如果你确实想要用户能够创建多条具有相同电子邮件地址的记录，但不是相同的名字、姓氏加上电子邮件地址，那么可以将其他路径添加到唯一键策略。 因此，不是仅基于电子邮件地址创建唯一键，而是基于名字、姓氏和电子邮件的组合创建唯一键。 在这种情况下，允许使用这三个路径的每个唯一组合，因此，数据库可能包含具有以下路径值的项。 其中每条记录都会通过唯一键策略。  

**firstName、lastName 和 email 的唯一键的允许值**

|名字|姓氏|电子邮件地址|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

如果你尝试使用上表中列出的任意组合插入另一条记录，则会收到一个错误，指示未满足唯一键约束。 Azure Cosmos DB 返回的错误是“已存在具有指定 ID 或名称的资源。” 或者“已存在具有指定 ID、名称或唯一索引的资源。” 

## <a name="using-unique-keys"></a>使用唯一键

创建容器时必须定义唯一键，并将唯一键限定为分区键。 若要基于前面的示例构建，如果基于邮政编码分区，则可以将表中的记录复制到每个分区中。

现有容器不能更新为使用唯一键。

一旦使用唯一键策略创建了容器，就无法更改策略，除非重新创建容器。 如果你有想要对其实施唯一键的现有数据，请创建新容器，然后使用相应的数据迁移工具将数据移到新容器。 对于 SQL 容器，请使用[数据迁移工具](import-data.md)。 对于 MongoDB 容器，请使用 [mongoimport.exe 或 mongorestore.exe](mongodb-migrate.md)。

每个唯一键可以包含最多 16 个路径值（例如 /firstName、/lastName、/address/zipCode 等）。 

每个唯一键策略可以具有最多 10 个唯一键约束或组合。 因此，使用名字、姓氏和电子邮件地址的上例就是一个约束，并且它使用了 16 个可能的可用路径中的三个。 

如果容器上存在唯一键策略，则用于创建、更新和删除项的请求单元费用会略高。 

不支持稀疏的唯一键。 如果某些唯一路径的值缺失，则会将其视为特殊的 null 值，这些值参与唯一性约束。

## <a name="sql-api-sample"></a>SQL API 示例

以下代码示例显示了如何创建具有两个唯一键约束的新 SQL 容器。 第一个约束是前面示例中所述的 firstName、lastName、email 约束。 第二个约束是用户地址/邮政编码。 使用此唯一键策略中路径的示例 JSON 文件遵循代码示例。 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipCode" } },

                }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

示例 JSON 文档。

```json
{
    "id": "1",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": [
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipCode": 98012
        }
    ],
}
```
## <a name="mongodb-api-sample"></a>MongoDB API 示例

下面的命令示例显示了如何在 MongoDB API 用户集合的 firstName、lastName 和 email 字段上创建唯一索引。 这将确保跨集合中所有文档的所有三个字段组合的唯一性。 对于 MongoDB API 集合，在创建集合之后但在填充集合之前创建唯一索引。

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```

## <a name="next-steps"></a>后续步骤

在本文中，你学习了如何为数据库中的项创建唯一键。 如果你是第一次创建容器，请查看[将 Azure Cosmos DB 中的数据分区](partition-data.md)作为相互依赖的唯一键和分区键。 


