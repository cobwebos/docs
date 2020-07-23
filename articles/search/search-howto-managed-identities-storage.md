---
title: 使用托管标识设置到存储帐户的连接（预览）
titleSuffix: Azure Cognitive Search
description: 了解如何使用托管标识设置到 Azure 存储帐户的索引器连接（预览）
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: ffbc850c580daee5890f9c75021cc518918d098e
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145379"
---
# <a name="set-up-a-connection-to-an-azure-storage-account-using-a-managed-identity-preview"></a>使用托管标识设置到 Azure 存储帐户的连接（预览）

> [!IMPORTANT] 
> 对于使用托管标识设置到数据源的连接的支持目前处于封闭的公开预览阶段。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。
> 可以填写[此表单](https://aka.ms/azure-cognitive-search/mi-preview-request)来请求访问预览版。

本页介绍如何使用托管标识设置到 Azure 存储帐户的索引器连接，而不是在数据源对象连接字符串中提供凭据。

在详细了解此功能之前，建议先了解什么是索引器以及如何为数据源设置索引器。 有关详细信息，请访问以下链接：
* [索引器概述](search-indexer-overview.md)
* [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
* [Azure 表索引器](search-howto-indexing-azure-tables.md)

## <a name="set-up-the-connection"></a>设置连接

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 打开系统分配的托管标识

启用系统分配的托管标识后，Azure 将为搜索服务创建一个标识，该标识可用于对同一租户和订阅中的其他 Azure 服务进行身份验证。 然后，可以在基于角色的访问控制 (RBAC) 分配中使用此标识，该分配允许在索引期间访问数据。

![打开系统分配的托管标识](./media/search-managed-identities/turn-on-system-assigned-identity.png "打开系统分配的托管标识")

选择“保存”后，将看到已分配给搜索服务的对象 ID。

![对象 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "对象 ID")
 
### <a name="2---add-a-role-assignment"></a>2 - 添加角色分配

在此步骤中，你将授予 Azure 认知搜索服务从存储帐户读取数据的权限。

1. 在 Azure 门户中，导航到包含要为其编制索引的数据的存储帐户。
2. 选择“访问控制(标识和访问管理)”
3. 依次选择“添加”和“添加角色分配” 

    ![添加角色分配](./media/search-managed-identities/add-role-assignment-storage.png "添加角色分配")

4. 根据要为其编制索引的存储帐户类型选择适当的角色：
    1. Azure Blob 存储要求将搜索服务添加到“读取器和数据访问”和“存储 Blob 数据读取器”角色 。
    1. Azure Data Lake Storage Gen2 要求将搜索服务添加到“读取器和数据访问”和“存储 Blob 数据读取器”角色 。
    1. Azure 表存储要求将搜索服务仅添加到“读取器和数据访问”角色。
5.  将“分配访问权限至”保留为“Azure AD 用户、组或服务主体” 
6.  搜索你的搜索服务，选中，然后选择“保存”

    ![添加读取器和数据访问角色分配](./media/search-managed-identities/add-role-assignment-reader-and-data-access.png "添加读取器和数据访问角色分配")

请注意，连接到 Azure blob 存储和 Azure Data Lake Storage Gen2 时，还必须添加“存储 Blob 数据读取器”角色分配。

![添加存储 Blob 数据读取器角色分配](./media/search-managed-identities/add-role-assignment-storage-blob-data-reader.png "添加存储 Blob 数据读取器角色分配")

### <a name="3---create-the-data-source"></a>3 - 创建数据源

从存储帐户编制索引时，数据源必须具有以下必需属性：

* **name** 是搜索服务中数据源的唯一名称。
* type
    * Azure Blob 存储：`azureblob`
    * Azure 表存储：`azuretable`
    * Azure Data Lake Storage Gen2：使用[此表单](https://aka.ms/azure-cognitive-search/mi-preview-request)注册预览版后，系统将提供“type”。
* **凭据**
    * 使用托管标识进行身份验证时，凭据格式与不使用托管标识时不同。 此时，需要提供没有帐户密钥或密码的 ResourceId。 ResourceId 必须包含存储帐户的订阅 ID、存储帐户的资源组和存储帐户名称。
    * 托管标识格式： 
        * ResourceId=/subscriptions/你的订阅 ID/resourceGroups/你的资源组名称/providers/Microsoft.Storage/storageAccounts/你的存储帐户名称/;*  *
* container 指定存储帐户中的容器或表名称。 默认情况下，容器中的所有 Blob 都可检索。 如果只想为特定虚拟目录中的 Blob 编制索引，可以使用可选的 **query** 参数指定该目录。

如何使用 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 创建 Blob 数据源对象的示例：

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name/;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}   
```

Azure 门户和 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) 也支持托管标识连接字符串。 Azure 门户需要一个功能标志，在使用此页顶部的链接注册预览版时，系统将提供该标志。 

### <a name="4---create-the-index"></a>4 - 创建索引

索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍了如何使用可搜索 `content` 字段创建索引，以存储从 Blob 中提取的文本：   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

有关创建索引的详细信息，请参阅[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - 创建索引器

索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。

创建索引和数据源后，便可以准备创建索引器。

Blob 索引器的索引器定义示例：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

此索引器每隔两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

有关定义索引器计划的详细信息，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="see-also"></a>另请参阅

详细了解 Azure 存储索引器：
* [Azure Blob 索引器](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2 索引器](search-howto-index-azure-data-lake-storage.md)
* [Azure 表索引器](search-howto-indexing-azure-tables.md)
