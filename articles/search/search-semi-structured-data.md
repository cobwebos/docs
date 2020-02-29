---
title: 教程：在 JSON blob 中索引半结构化数据
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 认知搜索 REST API 和 Postman 为半结构化 Azure JSON Blob 编制索引以及搜索此类数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: f025b3357943014a6d9c6e331c47f019fe94c5bf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196937"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>教程：使用 REST 为 Azure 存储中的 JSON blob 编制索引

Azure 认知搜索可使用一个知晓如何读取半结构化数据的[索引器](search-indexer-overview.md)来编制 Azure blob 存储中 JSON 文档和数组的索引。 半结构化数据包含用于分隔数据中的内容的标记或标签。 它的本质是提供必须全面索引的非结构化数据和符合数据模型的正式结构化数据之间的一个折中，例如可以按字段编制索引的关系数据库架构。

本教程使用 Postman 和[搜索 REST api](https://docs.microsoft.com/rest/api/searchservice/)来执行以下任务：

> [!div class="checklist"]
> * 为 Azure blob 容器配置 Azure 认知搜索数据源
> * 创建 Azure 认知搜索索引以包含可搜索的内容
> * 配置和运行索引器以读取容器和从 Azure blob 存储中提取可搜索内容
> * 搜索刚刚创建的索引

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

+ [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Postman 桌面应用](https://www.getpostman.com/)
+ [创建](search-create-service-portal.md)或[查找现有搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用本教程的免费服务。 免费搜索服务限制为三个索引、三个索引器和三个数据源。 本教程每样创建一个。 在开始之前，请确保你已在服务上实现了接受新资源的空间。

## <a name="download-files"></a>下载文件

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) 包含本教程使用的数据。 请下载此文件并将其解压缩到其自身的文件夹。 数据源自 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)，已为本教程转换为 JSON。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” **“密钥”中，获取有关该服务的完全权限的管理员密钥** > 。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="prepare-sample-data"></a>准备示例数据

1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户，单击“Blob”，然后单击“+ 容器”。

1. [创建一个 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)用于包含示例数据。 可将“公共访问级别”设为任何有效值。

1. 创建容器后，将其打开，然后在命令栏中选择“上传”。

   ![在命令栏上上传](media/search-semi-structured-data/upload-command-bar.png "在命令栏上上传")

1. 导航到包含示例文件的文件夹。 选择所有这些文件，然后单击“上传”。

   ![上传文件](media/search-semi-structured-data/clinicalupload.png "上传文件")

上传完成后，这些文件应会显示在数据容器内其自身的子文件夹中。

## <a name="set-up-postman"></a>设置 Postman

启动 Postman 并设置 HTTP 请求。 如果不熟悉此工具，请参阅[使用 Postman 探索 Azure 认知搜索 REST API](search-get-started-postman.md) 了解详细信息。

本教程中每个调用的请求方法是 **POST**。 标头键为“Content-type”和“api-key”。 上述标头键的值分别为“application/json”和你的“admin key”（“admin key”是搜索主密钥的占位符）。 正文是调用的实际内容的放置位置。 根据所用的客户端，在如何构造查询方面可能存在一些差异，但基本思路相同。

  ![半结构化搜索](media/search-semi-structured-data/postmanoverview.png)

我们将使用 Postman 向搜索服务发出三个 API 调用，以创建数据源、索引和索引器。 数据源包含指向存储帐户的指针以及 JSON 数据。 加载数据时，搜索服务会建立连接。

查询字符串必须指定 api-version，并且每个调用都应返回“201 已创建”。 用于使用 JSON 数组的正式版 api-version 为 `2019-05-06`。

从 REST 客户端执行以下三个 API 调用。

## <a name="create-a-data-source"></a>创建数据源

[创建数据源 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)创建一个 Azure 认知搜索对象，该对象指定要编制索引的数据。

此调用的终结点为 `https://[service name].search.windows.net/datasources?api-version=2019-05-06`。 请将 `[service name]` 替换为搜索服务的名称。 

对于此调用，请求正文必须包含存储帐户名称、存储帐户密钥和 Blob 容器名称。 可在 Azure 门户上存储帐户的“访问密钥”中找到存储帐户密钥。 下图显示了该位置：

  ![半结构化搜索](media/search-semi-structured-data/storagekeys.png)

在执行该调用之前，请务必替换调用正文中的 `[storage account name]`、`[storage account key]` 和 `[blob container name]`。

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

响应应如下所示：

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>创建索引
    
第二次调用的是[创建索引 API](https://docs.microsoft.com/rest/api/searchservice/create-index)，用于创建可存储所有可搜索数据的 Azure 认知搜索索引。 索引指定所有参数及其属性。

此调用的 URL 为 `https://[service name].search.windows.net/indexes?api-version=2019-05-06`。 请将 `[service name]` 替换为搜索服务的名称。

首先替换 URL。 然后，将以下代码复制并粘贴到正文，并运行查询。

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

响应应如下所示：

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>创建并运行索引器

索引器连接数据源，将数据导入目标搜索索引，并选择性地提供一个计划来自动执行数据刷新。 REST API 为[创建索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

此调用的 URL 为 `https://[service name].search.windows.net/indexers?api-version=2019-05-06`。 请将 `[service name]` 替换为搜索服务的名称。

首先替换 URL。 然后，将以下代码复制并粘贴到正文，并发送请求。 系统会立即处理该请求。 当响应返回时，便拥有了可进行全文搜索的索引。

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

响应应如下所示：

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>搜索 JSON 文件

加载第一个文档后，可立即开始搜索。 为完成此任务，在门户中使用[搜索浏览器](search-explorer.md)。

在 Azure 门户中，打开搜索服务的“概述”页，在“索引”列表中找到创建的索引。

请务必选择刚刚创建的索引。 

  ![非结构化搜索](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>用户定义的元数据搜索

如前所述，可通过多种方式查询数据：全文搜索、系统属性或用户定义的元数据。 仅当在创建目标索引期间已将系统属性和用户定义的元数据标记为`$select`可检索**时，才能使用**  参数搜索此类信息。 索引中的参数在创建后不可更改。 但是，可以添加更多的参数。

`$select=Gender,metadata_storage_size` 就是一个基本查询的例子，它会将返回结果限制为这两个参数。

  ![半结构化搜索](media/search-semi-structured-data/lastquery.png)

`$filter=MinimumAge ge 30 and MaximumAge lt 75` 是更复杂查询的例子，它只返回参数 MinimumAge 大于或等于 30 且参数 MaximumAge 小于 75 的结果。

  ![半结构化搜索](media/search-semi-structured-data/metadatashort.png)

请任意自行体验更多的查询。 可以学习逻辑运算符（and、or、not）和比较运算符（eq、ne、gt、lt、ge、le）的用法。 字符串比较区分大小写。

`$filter` 参数只适用于在创建索引时标记为可筛选的元数据。

## <a name="reset-and-rerun"></a>重置并重新运行

在开发的早期实验阶段，设计迭代的最实用方法是从 Azure 认知搜索中删除对象，并允许你的代码重新生成它们。 资源名称是唯一的。 删除某个对象后，可以使用相同的名称重新创建它。

可以使用门户删除索引、索引器和数据源。 或使用**DELETE**并为每个对象提供 url。 以下命令删除一个索引器。

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

成功删除后会返回状态代码 204。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时删除不再需要的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

使用左侧导航窗格中的 "所有资源" 或 "资源组" 链接，可以在门户中查找和管理资源。

## <a name="next-steps"></a>后续步骤

现在，你已熟悉 Azure Blob 索引的基本知识，接下来让我们详细了解索引器配置。

> [!div class="nextstepaction"]
> [配置 Azure Blob 存储索引器](search-howto-indexing-azure-blob-storage.md)