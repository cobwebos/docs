---
title: "索引器操作（Azure 搜索服务 REST API：2015-02-28-Preview）| Microsoft Docs"
description: "索引器操作（Azure 搜索服务 REST API：2015-02-28-Preview）"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 42b5f85c-8304-4bc7-8e1e-a9c76b8ca25a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/01/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 844abce7941b7285c88aa0eda9eca43e1e5e42da


---
# <a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>索引器操作（Azure 搜索服务 REST API：2015-02-28-Preview）
> [!NOTE]
> 本文介绍了 [2015-02-28-Preview REST API](search-api-2015-02-28-preview.md) 中的索引器。 此 API 版本添加了具有文档提取的 Azure Blob 存储索引器和 Azure 表存储索引器的预览版本以及其他改进。 API 还支持公开上市 (GA) 的索引器，包括适用于 Azure SQL 数据库、Azure VM 上的 SQL Server 和 Azure DocumentDB 的索引器。
> 
> 

## <a name="overview"></a>概述
Azure 搜索可以与某些常见数据源直接集成，无需编写代码即可编制数据索引。 若要完成此设置，你可以调用 Azure 搜索 API 创建和管理**索引器**和**数据源**。 

**索引器**是将数据源连接到目标搜索索引的资源。 可通过以下方式使用索引器： 

* 执行数据的一次性复制以填充索引。
* 在计划上同步索引与数据源中的更改。 计划是索引器定义的一部分。
* 按需调用以根据需要更新索引。 

如果你希望定期更新索引，则**索引器**很有用。 你可以设置一个内联计划作为索引器定义的一部分，也可以使用“运行索引器”[](#RunIndexer)按需运行它。 

**数据源**指定需要编制索引的数据、用于访问数据的凭据，以及用于启用 Azure 搜索的策略，以便有效地标识数据中的更改（如数据库表中已修改或已删除的行）。 它定义为独立的资源，以便可供多个索引器使用。

当前支持以下数据源：

* **Azure SQL 数据库**和 **Azure VM 上的 SQL Server**。 若要获取有针对性的演练，请参阅[本文](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。 
* **Azure DocumentDB**。 若要获取有针对性的演练，请参阅[本文](../documentdb/documentdb-search-indexer.md)。 
* **Azure Blob 存储**，包括以下文档格式：PDF、Microsoft Office（DOCX/DOC、XSLX/XLS、PPTX/PPT、MSG）、HTML、XML、ZIP 和纯文本文件（包括 JSON）。 若要获取有针对性的演练，请参阅[本文](search-howto-indexing-azure-blob-storage.md)。
* **Azure 表存储**。 若要获取有针对性的演练，请参阅[本文](search-howto-indexing-azure-tables.md)。

我们考虑在以后添加对其他数据源的支持。 若要帮助我们确定这些决策的优先级，请在 [Azure 搜索反馈论坛](http://feedback.azure.com/forums/263029-azure-search)上提供你的反馈。

有关与索引器和数据源资源相关的最大限制，请参阅[服务限制](search-limits-quotas-capacity.md)。

## <a name="typical-usage-flow"></a>典型用法流
可通过简单 HTTP 请求（POST、GET、PUT、DELETE）针对给定 `data source` 或 `indexer` 资源创建和管理索引器和数据源。

设置自动索引编制过程通常包含四个步骤：

1. 标识包含需要编制索引的数据的数据源。 请记住，Azure 搜索可能不支持数据源中的所有数据类型。 若要获取相关列表，请参阅[支持的数据类型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。
2. 创建其架构与你的数据源兼容的 Azure 搜索索引。
3. 根据[创建数据源](#CreateDataSource)中所述创建 Azure 搜索数据源。
4. 根据[创建索引器](#CreateIndexer)中所述创建 Azure 搜索索引器。

你应该计划为每个目标索引和数据源组合创建一个索引器。 你可以将多个索引器编写到同一索引中，也可以针对多个索引器重复使用同一数据源。 但是，一个索引器一次只能使用一个数据源，并且只能写入到单个索引。 

在创建索引器后，你可以使用“获取索引器状态”[](#GetIndexerStatus)操作检索其执行状态。 还可以使用“运行索引器”[](#RunIndexer)操作随时运行索引器（而不是按计划定期运行它）。

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>创建数据源
在 Azure 搜索中，数据源与索引器配合使用，提供有关目标索引的临时或计划数据刷新的连接信息。 可使用 HTTP POST 请求在 Azure 搜索服务内创建新数据源。

    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

此外，可使用 PUT 并在 URI 上指定数据源名称。 如果数据源不存在，则创建它。

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

> [!NOTE]
> 允许的最大数据源数因定价层而异。 免费服务允许最多 3 个数据源。 标准服务允许 50 个数据源。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。
> 
> 

**请求**

所有服务请求都需要 HTTPS。 **创建数据源**请求可使用 POST 或 PUT 方法构造。 使用 POST 时，必须在请求正文中提供数据源名称以及数据源定义。 对于 PUT，该名称是 URL 的一部分。 如果数据源不存在，则创建它。 如果已经存在，则将其更新为新定义。 

如果数据源名称必须是小写，请以字母或数字开头、不包含斜杠或点，并且少于 128 个字符。 数据源名称以字母或数字开头后，名称其余部分可包含任何字母、数字和短划线，前提是短划线不是连续的。 有关详细信息，请参阅[命名规则](https://msdn.microsoft.com/library/azure/dn857353.aspx)。

`api-version` 是必需的。 当前版本为 `2015-02-28`。

**请求标头**

以下列表介绍必需和可选的请求标头。 

* `Content-Type`：必需。 将其设置为 `application/json`
* `api-key`：必需。 `api-key` 用于对搜索服务的请求进行身份验证。 它是一个字符串值，对于服务是唯一的。 **创建数据源**请求必须包含一个设置为管理密钥（而不是查询密钥）的 `api-key` 标头。 

还需要服务名称才能构造请求 URL。 可从 [Azure 门户](https://portal.azure.com/)中的服务仪表板获取服务名称和 `api-key`。 有关页面导航帮助，请参阅[在门户中创建搜索服务](search-create-service-portal.md)。

<a name="CreateDataSourceRequestSyntax"></a>
**请求正文语法**

请求正文包含数据源定义，其中包括数据源的类型、用于读取数据的凭据，以及可选数据更改检测和数据删除检测策略，这些策略用于有效标识数据源中与定期计划索引器一起使用时更改或删除的数据。 

构造请求负载的语法如下。 本主题中进一步提供了示例请求。

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

请求包含以下属性： 

* `name`：必需。 数据源的名称。 数据源名称必须仅包含小写字母、数字或短划线，不能以短划线开头或结尾，长度限制为 128 个字符。
* `description`：可选说明。 
* `type`：必需。 必须是支持的数据源类型之一：
  * `azuresql` - Azure SQL 数据库或 Azure VM 上的 SQL Server
  * `documentdb` - Azure DocumentDB
  * `azureblob` - Azure Blob 存储
  * `azuretable` - Azure 表存储
* `credentials`：
  * 必需 `connectionString` 属性指定数据源的连接字符串。 连接字符串的格式取决于数据源类型： 
    * 对于 Azure SQL，这是常用的 SQL Server 连接字符串。 如果你要使用 Azure 门户检索连接字符串，请使用 `ADO.NET connection string` 选项。
    * 对于 DocumentDB，连接字符串必须采用以下格式：`"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`。 所有值都是必需的。 可以在 [Azure 门户](https://portal.azure.com/)中找到这些值。  
    * 对于 Azure Blob 和表存储，这是存储帐户连接字符串。 格式如[此处](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/)所述。 HTTPS 终结点协议是必需的。  
* `container`（必需）：指定要使用 `name` 和 `query` 属性编制索引的数据： 
  * `name`（必需）：
    * Azure SQL：指定表或视图。 你可以使用架构限定的名称，如 `[dbo].[mytable]`。
    * DocumentDB：指定集合。 
    * Azure Blob 存储：指定存储容器。
    * Azure 表存储：指定表的名称。 
  * `query`（可选）：
    * DocumentDB：允许你指定一个查询来将一个任意 JSON 文档平整成 Azure 搜索可编制索引的平面架构。  
    * Azure Blob 存储：允许你指定 blob 容器中的虚拟文件夹。 例如，对于 blob 路径 `mycontainer/documents/blob.pdf`，`documents` 可用作虚拟文件夹。
    * Azure 表存储：允许你指定筛选要导入的行集的查询。
    * Azure SQL：不支持查询。 如果你需要此功能，请为[此建议](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)投票
* 下面介绍了可选 `dataChangeDetectionPolicy` 和 `dataDeletionDetectionPolicy` 属性。

<a name="DataChangeDetectionPolicies"></a>
**数据更改检测策略**

数据更改检测策略旨在有效识别已更改的数据项。 支持的策略因数据源类型而异。 以下部分介绍了每个策略。 

***高使用标记更改检测策略*** 

如果你的数据源包含满足以下条件的列或属性，请使用此策略：

* 所有插入都为列指定一个值。 
* 对某个项目的所有更新也会更改该列的值。 
* 此列的值随每次更改增加。
* 可以有效执行使用类似于以下 `WHERE [High Water Mark Column] > [Current High Water Mark Value]` 的筛选子句的查询。

例如，在使用 Azure SQL 数据源时，已编制索引的 `rowversion` 列是可供与高使用标记策略一起使用的理想候选项。 

可按如下方式执行此策略：

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

在使用 DocumentDB 数据源时，你必须使用 DocumentDB 提供的 `_ts` 属性。 

在使用 Azure Blob 数据源时，Azure 搜索基于 blob 的上次修改时间戳自动使用高使用标记更改检测策略；你无需自行指定此类策略。   

***SQL 集成的更改检测策略***

如果你的 SQL 数据库支持[更改跟踪](https://msdn.microsoft.com/library/bb933875.aspx)，我们建议使用 SQL 集成的更改跟踪策略。 此策略支持最有效的更改跟踪，并允许 Azure 搜索标识删除的行，架构中无需具有显式的“软删除”列。

从以下 SQL Server 数据库版本开始，支持集成的更改跟踪： 

* SQL Server 2008 R2，如果你使用的是 Azure VM 上的 SQL Server。
* Azure SQL 数据库 V12，如果你使用的是 Azure SQL 数据库。  

当使用 SQL 集成的更改跟踪策略时，不指定单独的数据删除检测策略 - 此策略具有对标识删除的行的内置支持。 

此策略仅适用于表，不适用于视图。 需要先为要使用的表启用更改跟踪，然后才可以使用此策略。 有关说明，请参阅[启用和禁用更改跟踪](https://msdn.microsoft.com/library/bb964713.aspx)。    

构建**创建数据源**请求时，可按如下方式指定 SQL 集成的更改跟踪策略：

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**数据删除检测策略**

数据删除检测策略旨在有效识别已删除的数据项。 当前，唯一受支持的策略是 `Soft Delete` 策略，它允许根据 `soft delete` 列的值或数据源中的属性标识删除的项。 可按如下方式执行此策略：

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

> [!NOTE]
> 仅支持带有字符串、整数或布尔值的列。 用作 `softDeleteMarkerValue` 的值必须是字符串，即使相应的列包含整数或布尔值也是如此。 例如，如果数据源中显示的值为 1，则将 `"1"` 用作 `softDeleteMarkerValue`。    
> 
> 

<a name="CreateDataSourceRequestExamples"></a>
**请求正文示例**

如果你想要将数据源与按计划运行的索引器一起使用，此示例显示如何指定更改和删除检测策略： 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

如果你只想要针对数据的一次性副本使用数据源，可以忽略策略：

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**响应**

对于成功的请求：“201 已创建”。 

<a name="UpdateDataSource"></a>

## <a name="update-data-source"></a>更新数据源
你可以使用 HTTP PUT 请求更新现有数据源。 指定要在请求 URI 上更新的数据源的名称：

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` 是必需的。 当前版本为 `2015-02-28`。 [Azure 搜索 API 版本](https://msdn.microsoft.com/library/azure/dn864560.aspx)具有有关备用版本的详细信息。

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**请求**

请求正文语法与用于[创建数据源请求](#CreateDataSourceRequestSyntax)的语法相同。

无法在现有数据源上更新某些属性。 例如，你无法更改现有数据源的类型。  

如果你不想要更改现有数据源的连接字符串，则可以为连接字符串指定文本 `<unchanged>`。 这对于以下情况很有帮助：需要更新数据源，但因属于安全敏感型数据而不便于访问连接字符串。

**响应**

对于成功的请求：“201 已创建”（如果已创建新数据源）和“204 无内容”（如果已更新现有数据源）。

<a name="ListDataSource"></a>

## <a name="list-data-sources"></a>列出数据源
**列出数据源**操作返回 Azure 搜索服务中的数据源列表。 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

`api-version` 是必需的。 当前版本为 `2015-02-28`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的请求：“200 正常”。

下面是响应正文示例：

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

请注意，可仅将响应筛选为感兴趣的属性。 例如，如果只需要数据源名称的列表，请使用 OData `$select` 查询选项：

    GET /datasources?api-version=205-02-28&$select=name

在此情况下，来自上述示例的响应将如下所示： 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

如果在搜索服务中有大量数据源，这是节省带宽的有用技术。

<a name="GetDataSource"></a>

## <a name="get-data-source"></a>获取数据源
**获取数据源**操作从 Azure 搜索中获取数据源定义。

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

`api-version` 是必需的。 当前版本为 `2015-02-28`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

响应类似于[创建数据源示例请求](#CreateDataSourceRequestExamples)中的示例： 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [!NOTE]
> 当调用此 API 时，不要将 `Accept` 请求标头设置为 `application/json;odata.metadata=none`，因为此操作会导致 `@odata.type` 属性从响应中省略，并且你无法区分不同类型的数据更改和数据删除检测策略。 
> 
> 

<a name="DeleteDataSource"></a>

## <a name="delete-data-source"></a>删除数据源
**删除数据源**操作从 Azure 搜索服务中删除数据源。

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> 如果任何索引器引用你要删除的数据源，删除操作仍会继续。 但是，这些索引器将在下次运行时转换到错误状态。  
> 
> 

`api-version` 是必需的。 当前版本为 `2015-02-28`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：204 无内容”。

<a name="CreateIndexer"></a>

## <a name="create-indexer"></a>创建索引器
可使用 HTTP POST 请求在 Azure 搜索服务内创建新索引器。

    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

此外，可使用 PUT 并在 URI 上指定数据源名称。 如果数据源不存在，则创建它。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [!NOTE]
> 允许的最大索引器数因定价层而异。 免费服务允许最多 3 个索引器。 标准服务允许 50 个索引器。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md)。
> 
> 

`api-version` 是必需的。 当前版本为 `2015-02-28`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

<a name="CreateIndexerRequestSyntax"></a>
**请求正文语法**

请求正文包含索引器定义，可指定用于编制索引的数据源和目标索引以及可选的索引计划和参数。 

构造请求负载的语法如下。 本主题中进一步提供了示例请求。

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**索引器计划**

一个索引器可以选择指定一个计划。 如果存在计划，则索引器将按计划定期运行。 计划具有以下属性：

* `interval`：必需。 指定索引器运行的间隔或时段的持续时间值。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`"P[nD][T[nH][nM]]"`。 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。 
* `startTime`：必需。 索引器应开始运行时的 UTC 日期时间。 

**索引器参数**

索引器可以选择指定影响其行为的多个参数。 所有参数都是可选的。  

* `maxFailedItems`：索引器运行被视为失败前，允许索引失败的项目数。 默认值为 0。 [获取索引器状态](#GetIndexerStatus)操作将返回有关失败的项目的信息。 
* `maxFailedItemsPerBatch`：索引器运行被视为失败前，每批中允许索引失败的项目数。 默认值为 0。
* `base64EncodeKeys`：指定是否对文档密钥进行 base-64 编码。 Azure 搜索会对出现在文档密钥中的字符施加限制。 但是，你的源数据中的值可能包含无效的字符。 如果有必要将此类值的索引编制为文档密钥，可以将此标志设置为 true。 默认值为 `false`。
* `batchSize`：指定从数据源读取并将索引编制为单个批处理以提高性能的项数。 默认值取决于数据源类型：对于 Azure SQL 和 DocumentDB，默认值为 1000；对于 Azure Blob 存储，默认值为 10。

**字段映射**

字段映射可用于将数据源中的字段名称映射到目标索引中的不同字段名称。 例如，考虑具有字段 `_id` 的源表。 Azure 搜索不允许字段名称以下划线开头，必须重命名该字段。 可以使用索引器的 `fieldMappings` 属性完成此操作，如下所示： 

    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

可以指定多个字段映射： 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

源和目标字段名称不区分大小写。

<a name="FieldMappingFunctions"></a>
***字段映射函数***

字段映射还可用于使用*映射函数*转换源字段值。

当前仅支持一个此类函数：`jsonArrayToStringCollection`。 它将包含格式为 JSON 数组的字符串的字段解析到目标索引中的 Collection(Edm.String) 字段。 它尤其适用于与 Azure SQL 索引器配合使用，因为 SQL 不包含本机集合数据类型。 使用方法如下所示： 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

例如，如果源字段包含字符串 `["red", "white", "blue"]`，类型 `Collection(Edm.String)` 的目标字段将由 `"red"`、`"white"` 和 `"blue"` 这三个值填充。

请注意，`targetFieldName` 属性是可选的；如果省略，将使用 `sourceFieldName` 值。 

<a name="CreateIndexerRequestExamples"></a>
**请求正文示例**

下面的示例创建一个索引器，该索引器按计划（从 2015 年 1 月 1 日 UTC 开始，每小时运行一次）将数据从 `ordersds` 数据源引用的表复制到 `orders` 索引。 如果每批中索引失败的项目不超过 5 个并且索引失败的项目总计不超过 10 个，则每个索引器都将成功调用。 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**响应**

对于成功的请求，返回“201 已创建”。

<a name="UpdateIndexer"></a>

## <a name="update-indexer"></a>更新索引器
可使用 HTTP PUT 请求更新现有索引器。 指定要在请求 URI 上更新的索引器的名称：

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

`api-version` 是必需的。 当前版本为 `2015-02-28`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**请求**

请求正文语法与用于[创建索引器请求](#CreateIndexerRequestSyntax)的语法相同。

**响应**

对于成功的请求：“201 已创建”（如果已创建新索引器）和“204 无内容”（如果已更新现有索引器）。

<a name="ListIndexers"></a>

## <a name="list-indexers"></a>列出索引器
**列出索引器**操作返回 Azure 搜索服务中的索引器列表。 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


`api-version` 是必需的。 预览版为 `2015-02-28-Preview`。 [Azure 搜索版本控制](https://msdn.microsoft.com/library/azure/dn864560.aspx)具有有关备用版本的详细信息。

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的请求：“200 正常”。

下面是响应正文示例：

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

请注意，可仅将响应筛选为感兴趣的属性。 例如，如果只需要索引器名称的列表，请使用 OData `$select` 查询选项：

    GET /indexers?api-version=2014-10-20-Preview&$select=name

在此情况下，来自上述示例的响应将如下所示： 

    {
      "value" : [ { "name": "myindexer" } ]
    }

如果在搜索服务中有大量索引器，这是节省带宽的有用技术。

<a name="GetIndexer"></a>

## <a name="get-indexer"></a>获取索引器
**获取索引器**操作从 Azure 搜索获取索引器定义。

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

`api-version` 是必需的。 预览版为 `2015-02-28-Preview`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

该响应类似于[创建索引器示例请求](#CreateIndexerRequestExamples)中的示例： 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>

## <a name="delete-indexer"></a>删除索引器
**删除索引器**操作从 Azure 搜索服务中删除索引器。

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

删除索引器时，此时正在执行的索引器将完成运行，但不会计划更进一步的执行。 尝试使用不存在的索引器将导致 HTTP 状态代码“404 找不到”。 

`api-version` 是必需的。 预览版为 `2015-02-28-Preview`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：204 无内容”。

<a name="RunIndexer"></a>

## <a name="run-indexer"></a>运行索引器
除按计划定期运行以外，也可以通过“运行索引器”操作按需调用索引器： 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

`api-version` 是必需的。 预览版为 `2015-02-28-Preview`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：202 已接受”。

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>获取索引器状态
**获取索引器状态**操作检索索引器的当前状态和执行历史记录： 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


`api-version` 是必需的。 预览版为 `2015-02-28-Preview`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：200 正常”。

响应正文包含有关总体索引器运行状况状态、上次索引器调用以及最近索引器调用的历史记录（如果存在）的信息。 

示例响应正文如下所示： 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**索引器状态**

索引器状态可以是以下值之一：

* `running` 指示索引器正常运行。 请注意，某些索引器执行可能仍会失败，检查 `lastResult` 属性也是一个好方法。 
* `error` 指示索引器遇到了一个错误，需要用户手动更正。 例如，数据源凭据可能已过期，或者数据源或目标索引的架构已发生重大更改。 

**索引器执行结果**

索引器执行结果包含有关单个索引器执行的信息。 最新结果显示为索引器状态的 `lastResult` 属性。 其他最近结果（如果存在）将返回为索引器状态的 `executionHistory` 属性。 

索引器执行结果包含以下属性： 

* `status`：执行的状态。 有关详细信息，请参阅下面的[索引器执行状态](#IndexerExecutionStatus)。 
* `errorMessage`：失败执行的错误消息。 
* `startTime`：此执行开始时的 UTC 时间。
* `endTime`：此执行结束时的 UTC 时间。 如果执行仍在进行，则不设置此值。
* `errors`：项目级错误（如果有）的数组。 每个条目都包含文档密钥（`key` 属性）和错误消息（`errorMessage` 属性）。 
* `itemsProcessed`：索引器尝试在此执行期间编制索引的数据源项目（例如表行）的数目。 
* `itemsFailed`：在此执行期间失败的项目的数目。  
* `initialTrackingState`：对于第一个索引器执行，或者如果未在使用的数据源上启用数据更改跟踪策略，始终为 `null`。 如果启用了此类策略，在后续执行中，该值将指示此执行处理的第一个（最低）更改跟踪值。 
* `finalTrackingState`：如果未在使用的数据源上启用数据更改跟踪策略，始终为 `null`。 否则，指示此执行成功处理的最新（最高）更改跟踪值。 

<a name="IndexerExecutionStatus"></a>
**索引器执行状态**

索引器执行状态捕获单个索引器执行的状态。 它具有以下值：

* `success` 指示索引器执行已成功完成。
* `inProgress` 指示索引器执行正在进行。 
* `transientFailure` 指示索引器执行已失败。 有关详细信息，请参阅 `errorMessage` 属性。 失败可能需要也可能不需要用户手动修复 - 例如，修复数据源和目标索引之间的架构不兼容性需要用户操作，而临时数据源故障则不需要。 索引器调用（如果有）将按计划继续执行。 
* `persistentFailure` 指示索引器已失败，需要用户操作。 计划的索引器执行将停止。 解决此问题后，使用“重置索引器 API”重新启动计划的执行。 
* `reset` 指示索引器已通过调用“重置索引器 API”（见下文）重置。 

<a name="ResetIndexer"></a>

## <a name="reset-indexer"></a>重置索引器
**重置索引器**操作可重置与索引器关联的更改跟踪状态。 这允许你触发从头开始重新编制索引（例如，如果你的数据源架构已更改），或更改与索引器关联的数据源的数据更改检测策略。   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

`api-version` 是必需的。 预览版为 `2015-02-28-Preview`。 

`api-key` 必须是管理密钥（而不是查询密钥）。 若要了解有关密钥的详细信息，请参阅[搜索服务 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 中的身份验证部分。 [在门户中创建搜索服务](search-create-service-portal.md)介绍了如何获取在请求中使用的服务 URL 和密钥属性。

**响应**

对于成功的响应，返回“状态代码：204 无内容”。

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 数据类型与 Azure 搜索数据类型之间的映射
<table style="font-size:12">
<tr>
<td>SQL 数据类型</td>    
<td>允许的目标索引字段类型</td>
<td>说明</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>int、smallint、tinyint</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>real、float</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney、money<br>decimal<br>numeric
</td>
<td>Edm.String</td>
<td>Azure 搜索不支持将十进制类型转换为 Edm.Double，因为这可能会损失精度
</td>
</tr>
<tr>
<td>char、nchar、varchar、nvarchar</td>
<td>Edm.String<br/>集合 (Edm.String)</td>
<td>有关如何将字符串列转换为 Collection(Edm.String) 的详细信息，请参阅本文档中的[字段映射函数](#FieldMappingFunctions)</td>
</tr>
<tr>
<td>smalldatetime、datetime、datetime2、date、datetimeoffset</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>geography</td>
<td>Edm.GeographyPoint</td>
<td>仅支持具有 SRID 4326（这是默认值）的类型 POINT 的地理实例</td>
</tr>
<tr>
<td>rowversion</td>
<td>不适用</td>
<td>行版本列不能存储在搜索索引中，但可用于更改跟踪</td>
</tr>
<tr>
<td>time、timespan<br>binary、varbinary、image、<br>xml、geometry、CLR 类型</td>
<td>不适用</td>
<td>不支持</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON 数据类型与 Azure 搜索数据类型之间的映射
<table style="font-size:12">
<tr>
<td>JSON 数据类型</td>    
<td>允许的目标索引字段类型</td>
<td>说明</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean、Edm.String</td>
<td></td>
</tr>
<tr>
<td>整数数字</td>
<td>Edm.Int32、Edm.Int64、Edm.String</td>
<td></td>
</tr>
<tr>
<td>浮点数字</td>
<td>Edm.Double、Edm.String</td>
<td></td>
</tr>
<tr>
<td>字符串</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>基元类型的数组，如 [“a”、“b”、“c”]</td>
<td>集合 (Edm.String)</td>
<td></td>
</tr>
<tr>
<td>类似于日期的字符串</td>
<td>Edm.DateTimeOffset、Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON 点对象</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON 点是采用以下格式的 JSON 对象：{ "type" : "Point", "coordinates" : [long, lat] } </td>
</tr>
<tr>
<td>其他 JSON 对象</td>
<td>不适用</td>
<td>不支持；Azure 搜索当前仅支持基元类型和字符串集合</td>
</tr>
</table>



<!--HONumber=Nov16_HO3-->


