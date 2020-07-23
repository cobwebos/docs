---
title: 使用托管标识设置到 Cosmos DB 帐户的连接（预览）
titleSuffix: Azure Cognitive Search
description: 了解如何使用托管标识设置到 Cosmos DB 帐户的索引器连接（预览）
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 107cd113645a2cbd4b452f9350fa67d734ee6df8
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143645"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>使用托管标识设置到 Cosmos DB 数据库的索引器连接（预览）

> [!IMPORTANT] 
> 对于使用托管标识设置到数据源的连接的支持目前处于封闭的公开预览阶段。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。
> 可以填写[此表单](https://aka.ms/azure-cognitive-search/mi-preview-request)来请求访问预览版。

本页描述如何使用托管标识设置到 Azure Cosmos DB 数据库的索引器连接，而不是在数据源对象连接字符串中提供凭据。

在详细了解此功能之前，建议先了解什么是索引器以及如何为数据源设置索引器。 有关详细信息，请访问以下链接：
* [索引器概述](search-indexer-overview.md)
* [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>使用托管标识设置连接

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 打开系统分配的托管标识

启用系统分配的托管标识后，Azure 将为搜索服务创建一个标识，该标识可用于对同一租户和订阅中的其他 Azure 服务进行身份验证。 然后，可以在基于角色的访问控制 (RBAC) 分配中使用此标识，该分配允许在编制索引期间访问数据。

![打开系统分配的托管标识](./media/search-managed-identities/turn-on-system-assigned-identity.png "打开系统分配的托管标识")

选择“保存”后，将看到已分配给搜索服务的对象 ID。

![对象 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "对象 ID")
 
### <a name="2---add-a-role-assignment"></a>2 - 添加角色分配

在此步骤中，你将授予 Azure 认知搜索服务从 Cosmos DB 数据库读取数据的权限。

1. 在 Azure 门户中，导航到包含要为其编制索引的数据的 Cosmos DB 帐户。
2. 选择“访问控制(标识和访问管理)”
3. 依次选择“添加”和“添加角色分配” 

    ![添加角色分配](./media/search-managed-identities/add-role-assignment-cosmos-db.png "添加角色分配")

4. 选择“Cosmos DB 帐户读者角色”
5. 将“分配访问权限至”保留为“Azure AD 用户、组或服务主体” 
6. 搜索你的搜索服务，选中它，然后选择“保存”

    ![添加读者和数据访问角色分配](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "添加读者和数据访问角色分配")

### <a name="3---create-the-data-source"></a>3 - 创建数据源

**数据源**指定要编制索引的数据、凭据和用于识别数据更改（如修改或删除了集合内的文档）的策略。 数据源定义为独立的资源，以便它可以被多个索引器使用。

使用托管标识对数据源进行身份验证时，凭据将不包括帐户密钥。

如何使用 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 创建 Cosmos DB 数据源对象的示例：

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

请求正文包含数据源定义，其中应包括以下字段：

| 字段   | 说明 |
|---------|-------------|
| name | 必需。 选择任意名称来表示你的数据源对象。 |
|type| 必需。 必须是 `cosmosdb`。 |
|**凭据** | 必需。 <br/><br/>使用托管标识进行连接时，凭据格式应为：*Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId 格式：ResourceId=/subscriptions/你的订阅 ID/resourceGroups/你的资源组名称/providers/Microsoft.DocumentDB/databaseAccounts/你的 cosmos db 帐户名称/;*  *<br/><br/>对于 SQL 集合，连接字符串不需要 ApiKind。<br/><br/>对于 MongoDB 集合，将 ApiKind=MongoDb 添加到连接字符串。 <br/><br/>对于 Gremlin 图和 Cassandra 表，注册[封闭索引器预览](https://aka.ms/azure-cognitive-search/indexer-preview)以获取对预览版的访问权限，以及有关如何设置凭据格式的信息。<br/>|
| **容器** | 包含下列元素： <br/>**名称**：必需。 指定要编制索引的数据库集合的 ID。<br/>**查询**：可选。 可以指定一个查询来将一个任意 JSON 文档平整成 Azure 认知搜索可编制索引的平面架构。<br/>对于 MongoDB API、Gremlin API 和 Cassandra API，不支持查询。 |
| **dataChangeDetectionPolicy** | 建议 |
|**dataDeletionDetectionPolicy** | 可选 |

Azure 门户和 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) 也支持托管标识连接字符串。 Azure 门户需要一个功能标志，在使用此页顶部的链接注册预览版时，系统将提供该标志。 

### <a name="4---create-the-index"></a>4 - 创建索引

索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍如何使用可搜索的 `booktitle` 字段创建索引：

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

有关创建索引的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - 创建索引器

索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。

创建索引和数据源后，就可以准备创建索引器了。

索引器定义示例：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

此索引器每隔两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

有关定义索引器计划的详细信息，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="see-also"></a>另请参阅

有关 Cosmos DB 索引的详细信息，请参阅：
* [Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)
