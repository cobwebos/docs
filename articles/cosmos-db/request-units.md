---
title: 请求单位和估计吞吐量 - Azure Cosmos DB | Microsoft Docs
description: 了解如何理解、指定和估计 Azure Cosmos DB 中的请求单位需求。
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: ''
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: 3679aa76d4a6b9fd6335371e1639f1f246867fa5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的请求单位数
现已推出：Azure Cosmos DB [请求单位计算器](https://www.documentdb.com/capacityplanner)。 了解[估计吞吐量需求](request-units.md#estimating-throughput-needs)。

![吞吐量计算器][5]

## <a name="introduction"></a>介绍
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 由 Microsoft 提供，是全球分布式多模型数据库。 使用 Azure Cosmos DB，无需租用虚拟机、部署软件或监视数据库。 Azure Cosmos DB 由 Microsoft 顶尖工程师操作和持续监视，以提供一流的可用性、性能和数据保护。 可以使用自己选择的 API 访问数据，例如 [SQL API](documentdb-introduction.md)、[MongoDB API](mongodb-introduction.md)、[表 API ](table-introduction.md)以及通过[图形 API](graph-introduction.md) 使用 Gremlin，这些都是本机支持的。 Azure Cosmos DB 的货币是请求单位 (RU)。 借助 RU，无需保留读取/写入容量或预配 CPU、内存和 IOPS。

Azure Cosmos DB 支持不同操作（范围从简单读取、写入到复杂图形查询等）的许多 API。 并非所有请求都是相同的，因此系统会根据请求所需的计算量为它们分配规范化数量的请求单位。 操作的请求单位数是确定性的，可以通过响应标头跟踪 Azure Cosmos DB 中的任何操作消耗的请求单位数。 

若要提供可预测的性能，需要以 100 RU/秒为单位保留吞吐量。 

阅读本文之后，能够回答以下问题：  

* 什么是请求单位和请求费用？
* 如何指定容器的请求单位容量？
* 如何评估应用程序的请求单位需求？
* 如果超过容器的请求单位容量会发生什么情况？

由于 Azure Cosmos DB 是多模型数据库，因此请务必注意，本文针对文档 API 引用集合/文档，针对图形 API 引用图形/节点，针对表 API 引用表/实体。 本文引用集合、图形或表的概念并将其视为容器，还引用文档、节点或实体的概念并将其视为项。

## <a name="request-units-and-request-charges"></a>请求单位和请求费用
Azure Cosmos DB 通过保留资源提供了快速且可预测的性能，以满足应用程序的吞吐量需求。  由于应用程序加载和访问模式会随着时间推移而更改，借助 Azure Cosmos DB，可以轻松增加或减少保留供应用程序使用的吞吐量。

通过 Azure Cosmos DB，可根据每秒处理的请求单位指定保留的吞吐量。 可以将请求单位视为吞吐量货币，因此，可以保留每秒可用于应用程序的定量有保障请求单位。  Azure Cosmos DB 中的每个操作（编写文档、执行查询、更新文档）都会消耗 CPU、内存和 IOPS。  也就是说，每个操作都会产生请求费用（用请求单位表示）。  要了解影响请求单位费用的因素，以及应用程序吞吐量要求，才能尽可能有效地运行应用程序。 Azure 门户中的数据资源管理器也是用于测试查询核心的有用工具。

建议通过观看以下视频入门，在视频中 Aravind Ramachandran 介绍 Azure Cosmos DB 的请求单位和可预测性能。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>指定 Azure Cosmos DB 中的请求单位容量
启用新容器时，指定希望保留的每秒请求单位数（每秒 RU 数）。 Azure Cosmos DB 会根据预配的吞吐量分配物理分区来托管容器，并拆分/重新均衡分区中不断增长的数据。

可以“固定”或“无限制”模式创建 Azure Cosmos DB 容器。 固定大小的容器最大限制为 10 GB，10,000 RU/s 吞吐量。 若要创建无限制容器，必须指定最低 1,000 RU/秒的吞吐量和一个[分区键](partition-data.md)。 由于数据可能需要跨多个分区拆分，因此需要选择一个基数较高（一百到几百万个非重复值）的分区键。 通过选择具有大量非重复值的分区键，可以确保 Azure Cosmos DB 能够统一缩放容器/表/图形与请求。 

> [!NOTE]
> 分区键是一个逻辑边界而不是物理边界。 因此，不需要限制非重复分区键值的数目。 事实上，分区键值宁多勿少，因为 Azure Cosmos DB 提供的负载均衡选项较多。

以下代码片段使用 .NET SDK 创建每秒 3,000 个请求单位的容器：

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure Cosmos DB 运行一个保留模型来预配吞吐量。 也就是说，用户需要根据保留的吞吐量付费，不管实际使用的吞吐量是多少。 随着应用程序的负载、数据和使用情况模式的更改，可以通过 SDK 或使用 [Azure 门户](https://portal.azure.com)轻松增加和减少保留的 RU 量。

将每个容器映射到 Azure Cosmos DB 中的 `Offer` 资源，该资源包含有关预配吞吐量的元数据。 可以通过查找容器的相应服务资源，并使用新的吞吐量值来对它进行更新，来更改分配的吞吐量。 以下代码片段使用 .NET SDK 将容器的吞吐量更改为每秒 5,000 个请求单位：

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

更改吞吐量不会影响容器的可用性。 通常，新的保留吞吐量在几秒内就会在应用程序上生效。

## <a name="throughput-isolation-in-globally-distributed-databases"></a>全局分布式数据库中的吞吐量隔离

将数据库复制到多个区域后，Azure Cosmos DB 将提供吞吐量隔离，以确保一个区域中的 RU 使用不会影响另一个区域中的 RU 使用。 例如，如果将数据写入到一个区域，并从另一个区域读取数据，用于在区域 A 中执行写入操作的 RU 不会减损用于区域 B 中读取操作的 RU。RU 不会拆分到已部署的所有区域中。 数据库所复制到的每个区域预配了全部数量的 RU。 有关全局复制的详细信息，请参阅[如何使用 Azure Cosmos DB 进行全局数据分配](distribute-data-globally.md)。

## <a name="request-unit-considerations"></a>请求单位注意事项
在估计为 Azure Cosmos DB 容器保留的请求单位数量时，请务必考虑以下变量：

* 项大小。 随着大小的增加，用来读取或写入数据的单位数也随之增加。
* 项属性计数。 假设默认为所有属性创建索引，用于写入文档/节点/实体的单位数将随着属性计数的增加而增加。
* **数据一致性**。 当使用“强”或“有限过期”的数据一致性级别时，读取项会占用更多单位数。
* **已创建索引的属性**。 每个容器的索引策略都可确定默认情况下要进行索引的属性类别。 通过限制已创建索引的属性数目或通过启用延迟索引编制，可以减少请求单位消耗。
* **文档索引**。 默认情况下会自动为每个项创建索引。 如果选择不为某些项创建索引，则使用的请求单位数将会减少。
* **查询模式**。 查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、投影、UDF 数和源数据集的大小都会影响查询操作的成本。
* **脚本使用情况**。  正如查询一样，存储过程和触发器也是根据所执行的操作的复杂性来使用请求单位的。 在开发应用程序时，检查请求费用标头，以更好地了解每个操作消耗请求单位容量的方式。

## <a name="estimating-throughput-needs"></a>估计吞吐量需求
请求单位是请求处理成本的规范化的度量。 单个请求单位用于表示读取（通过自链接或 ID）一个包含 10 个唯一属性值（系统属性除外）的 1 KB 项所需的处理容量。 插入、替换或删除同一项的请求要占用服务的更多处理，因此需要更多请求单位。   

> [!NOTE]
> 用于 1 KB 项的 1 个请求单位基线通过自链接或项的 ID 与简单的 GET 对应。
> 
> 

例如，下表显示了三种不同的项大小（1 KB、4 KB 和 64 KB）和两个不同的性能级别（500 次读取/秒 + 100 次写入/秒和 500 次读取/秒 + 500 次写入/秒）所要设置的请求单位数。 数据一致性配置为会话级别，索引策略设置为无。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p>项大小<strong></strong></p></td>
            <td valign="top"><p><strong>读取数/秒</strong></p></td>
            <td valign="top"><p><strong>写入数/秒</strong></p></td>
            <td valign="top"><p><strong>请求单位</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1,000 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3,000 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1,350 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4,150 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/秒</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>使用请求单位计算器
若要帮助客户微调其吞吐量估算，可以使用一个基于 Web 的[请求单位计算器](https://www.documentdb.com/capacityplanner)来帮助估计典型操作的请求单位要求，包括：

* 项创建（写入）数
* 项读取数
* 项删除数
* 项更新数

此工具也支持根据提供的示例项预估数据存储需求。

该工具易于使用：

1. 上传一个或多个具有代表性的项。
   
    ![将项上传到请求单位计算器][2]
2. 若要预估数据存储需求，请输入预期要存储的项总数。
3. 输入所需的项创建、读取、更新和删除操作数目（以秒为单位）。 若要预估项更新操作的请求单位费用，请上传上述步骤 1 中包含典型字段更新的示例项的副本。  例如，如果项更新通常会修改名为 lastLogin 和 userVisits 的两个属性，则只要复制示例项、更新这两个属性的值，并上传复制的项。
   
    ![在请求单位计算器中输入吞吐量要求][3]
4. 单击“计算”，并查看结果。
   
    ![请求单位计算器结果][4]

> [!NOTE]
> 如果有多种项类型，它们的索引属性大小和数目截然不同，则将每种类型的典型项的示例上传到该工具，并计算结果。
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>使用 Azure Cosmos DB 请求费用响应标头
每个来自 Azure Cosmos DB 服务的响应都包含一个自定义标头 (`x-ms-request-charge`)，该标头包含请求消耗的请求单位数。 此标头还可通过 Azure Cosmos DB SDK 进行访问。 在 .NET SDK 中，RequestCharge 是 ResourceResponse 对象的属性。  对于查询，Azure 门户中的 Azure Cosmos DB 数据资源管理器提供了用于执行的查询的请求费用信息。

基于这一点，有一种方法可以估计应用程序所需的保留的吞吐量：记录与针对应用程序所使用的代表性项运行典型操作相关联的请求单位费用，并估计预计每秒执行的操作数。  也要确保测算并包含典型查询和 Azure Cosmos DB 脚本使用情况。

> [!NOTE]
> 如果有多种项类型，它们的索引属性大小和数目截然不同，则记录与每种类型的典型项相关联的适用操作请求单位费用。
> 
> 

例如：

1. 记录创建（插入）典型项的请求单位费用。 
2. 记录读取典型项的请求单位费用。
3. 记录更新典型项的请求单位费用。
4. 记录典型、常见项查询的请求单位费用。
5. 记录应用程序使用的任何自定义脚本（存储的过程、触发器、用户自定义函数）的请求单位费用。
6. 根据给定的预计每秒运行的操作估计数计算所需的请求单位。

## <a id="GetLastRequestStatistics"></a>使用 API for MongoDB 的 GetLastRequestStatistics 命令
MongoDB API 支持使用自定义命令 *getLastRequestStatistics* 来检索指定操作的请求费用。

例如，在 Mongo Shell 中，执行所需的操作来验证请求费用。
```
> db.sample.find()
```

接下来，执行命令 *getLastRequestStatistics*。
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

基于这一点，有一种方法可以估计应用程序所需的保留的吞吐量：记录与针对应用程序所使用的代表性项运行典型操作相关联的请求单位费用，并估计预计每秒执行的操作数。

> [!NOTE]
> 如果有多种项类型，它们的索引属性大小和数目截然不同，则记录与每种类型的典型项相关联的适用操作请求单位费用。
> 
> 

## <a name="use-mongodb-api-portal-metrics"></a>使用 MongoDB API 门户指标
准确估算 MongoDB API 数据库请求单位费用的最简单方法是使用 [Azure 门户](https://portal.azure.com)指标。 使用“请求数”和“请求费用”图表，可以估算每个操作消耗的请求单位数，以及每个操作相对于其他操作消耗的请求单位数。

![MongoDB API 门户指标][6]

## <a name="a-request-unit-estimation-example"></a>请求单位估计示例
请考虑以下 ~1 KB 文档：

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> 文档在 Azure Cosmos DB 中已压缩，所以系统计算的上述文档的大小略小于 1 KB。
> 
> 

下表显示了此项的典型操作的请求单位大概费用（假定帐户一致性级别设置为“会话”且所有项都自动编制索引）：

| Operation | 请求单位费用 |
| --- | --- |
| 创建项目 |~15 RU |
| 读取项 |~1 RU |
| 按 ID 查询项 |~2.5 RU |

此外，此表还显示了应用程序所用的请求单位大概费用：

| 查询 | 请求单位费用 | 返回的项数 |
| --- | --- | --- |
| 按 ID 选择食品 |~2.5 RU |1 |
| 按制造商选择食物 |~7 RU |7 |
| 按食品组选择并按重量排序 |~70 RU |100 |
| 选择食品组中的前 10 个食品 |~10 RU |10 |

> [!NOTE]
> RU 费用取决于返回的项数。
> 
> 

使用此信息，可以根据预计的每秒操作和查询数量来估计此应用程序的 RU 需求：

| 操作/查询 | 每秒的估计数量 | 所需的 RU |
| --- | --- | --- |
| 创建项目 |10 |150 |
| 读取项 |100 |100 |
| 按制造商选择食物 |25 |175 |
| 按食品组进行选择 |10 |700 |
| 选择前 10 个 |15 |总计 150 |

在此示例中，预计的平均吞吐量需求为 1,275 RU/s。  舍入到最接近的百位数，我们会将此应用程序的容器设置为 1,300 RU/s。

## <a id="RequestRateTooLarge"></a>超过 Azure Cosmos DB 中保留的吞吐量限制
前面提到，如果预算为空，请求单位消耗以每秒速率进行评估。 对于超过容器预配的请求单位速率的应用程序，将限制对该容器的请求数，直到速率降低到保留级别之下。 被限制时，服务器将抢先结束请求、引发 RequestRateTooLargeException（HTTP 状态代码 429）并返回 x-ms-retry-after-ms 标头，该标头指示重试请求前用户必须等待的时间（以毫秒为单位）。

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

如果使用 .NET 客户端 SDK 和 LINQ 查询，则多数情况下无需处理此异常，因为 .NET 客户端 SDK 的当前版本会隐式捕获此响应，遵循服务器指定的 retry-after 标头，然后重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

如果存在多个高于请求速率的请求操作，则默认重试行为可能无法满足需要，这时客户端就会向应用程序引发 DocumentClientException，其状态代码为 429。 在这种情况下，可以考虑处理重试行为和应用程序错误处理例程中的逻辑，或为容器增加保留的吞吐量。

## <a id="RequestRateTooLargeAPIforMongoDB"></a>超过 MongoDB API 中保留的吞吐量限制
超过为容器预配的请求单位数的应用程序将受到限制，直到比率下降到保留级别以下。 受限制时，后端将提前结束请求并返回 *16500* 错误代码 -“请求过多”。 默认情况下，在返回“请求过多”错误代码之前，MongoDB API 会自动重试最多 10 次。 如果收到大量的“请求过多”错误代码，可以考虑在应用程序的错误处理例程中添加重试行为，或者[提高容器的保留吞吐量](set-throughput.md)。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure Cosmos DB 数据库的保留吞吐量的详细信息，请浏览以下资源：

* [Azure Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Azure Cosmos DB 中的分区数据](partition-data.md)

若要了解有关 Azure Cosmos DB 的详细信息，请参阅 Azure Cosmos DB [文档](https://azure.microsoft.com/documentation/services/cosmos-db/)。 

若要开始使用 Azure Cosmos DB 进行规模和性能测试，请参阅[使用 Azure DocumentDB 进行性能和规模测试](performance-testing.md)。

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/api-for-mongodb-metrics.png
