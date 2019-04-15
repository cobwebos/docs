---
title: 教程：为 JSON Blob 中的半结构化数据编制索引 - Azure 搜索
description: 了解如何使用 Azure 搜索和 Postman 为半结构化 Azure JSON Blob 编制索引以及搜索此类数据。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4df64595f83bd7280fa781f27f3030eda3729911
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471454"
---
# <a name="tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>教程：在 Azure 搜索中为半结构化数据 (JSON Blob) 编制索引以及搜索此类数据

Azure 搜索可使用一个知晓如何读取半结构化数据的[索引器](search-indexer-overview.md)来编制 Azure blob 存储中 JSON 文档和数组的索引。 半结构化数据包含用于分隔数据中的内容的标记或标签。 它的本质是提供必须全面索引的非结构化数据和符合数据模型的正式结构化数据之间的一个折中，例如可以按字段编制索引的关系数据库架构。

在本教程中，使用 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/) 和 REST 客户端执行以下任务：

> [!div class="checklist"]
> * 为 Azure blob 容器配置 Azure 搜索数据源
> * 创建 Azure 搜索索引以包含可搜索的内容
> * 配置和运行索引器以读取容器和从 Azure blob 存储中提取可搜索内容
> * 搜索刚刚创建的索引

> [!NOTE]
> 本教程依赖于 JSON 数组支持，该项当前是 Azure 搜索中的预览功能。 该功能在门户中不可用。 为此，我们会使用可提供此功能的预览版 REST API，并使用某个 REST 客户端工具来调用该 API。

## <a name="prerequisites"></a>先决条件

本快速入门使用以下服务、工具和数据。 

[创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本教程中使用免费服务。 

[创建一个 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)用于存储示例数据。

使用 [Postman 桌面应用](https://www.getpostman.com/)将请求发送到 Azure 搜索。

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) 包含本教程使用的数据。 请下载此文件并将其解压缩到其自身的文件夹。 数据源自 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)，已为本教程转换为 JSON。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-fiddler/get-url-key.png "Get an HTTP endpoint and access key")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="prepare-sample-data"></a>准备示例数据

1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户，单击“Blob”，然后单击“+ 容器”。

1. [创建一个 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)用于包含示例数据。 由于你将使用密钥和存储帐户名称进行连接，因此请确保容器的“公共访问级别”设置为“容器(对容器的匿名读取访问权限)”。

   ![设置公共访问级别](media/search-semi-structured-data/container-public-access-level.png "设置公共访问级别")

1. 创建容器后，将其打开，然后在命令栏中选择“上传”。

   ![在命令栏中上传](media/search-semi-structured-data/upload-command-bar.png "在命令栏中上传")

1. 导航到包含示例文件的文件夹。 选择所有这些文件，然后单击“上传”。

   ![上传文件](media/search-semi-structured-data/clinicalupload.png "上传文件")

上传完成后，这些文件应会显示在数据容器内其自身的子文件夹中。

## <a name="set-up-postman"></a>设置 Postman

启动 Postman 并设置 HTTP 请求。 如果不熟悉此工具，请参阅[使用 Postman 探索 Azure 搜索 REST API](search-fiddler.md) 了解详细信息。

本教程中每个调用的请求方法是 **POST**。 标头键为“Content-type”和“api-key”。 上述标头键的值分别为“application/json”和你的“admin key”（“admin key”是搜索主密钥的占位符）。 正文是调用的实际内容的放置位置。 根据所用的客户端，在如何构造查询方面可能存在一些差异，但基本思路相同。

  ![半结构化搜索](media/search-semi-structured-data/postmanoverview.png)

我们将使用 Postman 向搜索服务发出三个 API 调用，以创建数据源、索引和索引器。 数据源包含指向存储帐户的指针以及 JSON 数据。 加载数据时，搜索服务会建立连接。

查询字符串必须包含一个预览 API（如 api-version=2017-11-11-Preview），每个调用应返回“201 Created”。 正式版 api-version 尚未提供将 json 作为 jsonArray 进行处理的功能，目前只有预览版 api-version 提供此功能。

从 REST 客户端执行以下三个 API 调用。

## <a name="create-a-data-source"></a>创建数据源

[创建数据源 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 可创建一个 Azure 搜索对象，用于指定要编制索引的数据。

此调用的终结点为 `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`。 请将 `[service name]` 替换为搜索服务的名称。 

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
    
第二次调用的是[创建索引 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，用于创建可存储所有可搜索数据的 Azure 搜索索引。 索引指定所有参数及其属性。

此调用的 URL 为 `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`。 请将 `[service name]` 替换为搜索服务的名称。

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

此调用的 URL 为 `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`。 请将 `[service name]` 替换为搜索服务的名称。

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

请务必选择刚刚创建的索引。 API 版本可以是预览版或正式版。 唯一的预览版要求是为 JSON 数组编制索引。

  ![非结构化搜索](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>用户定义的元数据搜索

如前所述，可通过多种方式查询数据：全文搜索、系统属性或用户定义的元数据。 仅当在创建目标索引期间已将系统属性和用户定义的元数据标记为**可检索**时，才能使用 `$select` 参数搜索此类信息。 索引中的参数在创建后不可更改。 但是，可以添加更多的参数。

`$select=Gender,metadata_storage_size` 就是一个基本查询的例子，它会将返回结果限制为这两个参数。

  ![半结构化搜索](media/search-semi-structured-data/lastquery.png)

`$filter=MinimumAge ge 30 and MaximumAge lt 75` 是更复杂查询的例子，它只返回参数 MinimumAge 大于或等于 30 且参数 MaximumAge 小于 75 的结果。

  ![半结构化搜索](media/search-semi-structured-data/metadatashort.png)

请任意自行体验更多的查询。 可以学习逻辑运算符（and、or、not）和比较运算符（eq、ne、gt、lt、ge、le）的用法。 字符串比较区分大小写。

`$filter` 参数只适用于在创建索引时标记为可筛选的元数据。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，最快的清理方式是删除包含 Azure 搜索服务的资源组。 现在，可以删除资源组以永久删除其中的所有内容。 在门户中，资源组名称显示在 Azure 搜索服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

可将基于 AI 的认知服务算法附加到索引器管道。 下一步，继续学习下面的教程。

> [!div class="nextstepaction"]
> [使用 AI 编制索引](cognitive-search-tutorial-blob.md)