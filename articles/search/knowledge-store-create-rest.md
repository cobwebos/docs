---
title: 使用 REST 创建知识存储（预览版）
titleSuffix: Azure Cognitive Search
description: 使用 REST API 和 Postman 创建 Azure 认知搜索知识存储，用于保存 AI 扩充管道的扩充内容。 此功能目前处于公开预览状态。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 4d9810b9075bc3049758e03ba8376621661b79ba
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563218"
---
# <a name="create-an-azure-cognitive-search-knowledge-store-by-using-rest"></a>使用 REST 创建 Azure 认知搜索知识存储

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

Azure 认知搜索中的知识存储功能可以保留 AI 扩充管道的输出供后续分析或进行其他下游处理。 AI 扩充的管道接受图像文件或非结构化文本文件，使用 Azure 认知搜索为其编制索引，应用 Azure 认知服务中的 AI 扩充（例如图像分析和自然语言处理），并将结果保存到 Azure 存储中的知识存储。 可以在 Azure 门户中使用 Power BI 或存储资源管理器等工具来浏览知识存储。

在本文中，你将使用 REST API 接口基于一系列酒店评论引入、编制索引和应用 AI 扩充。 酒店评论已导入到 Azure Blob 存储中。 结果已作为知识存储保存在 Azure 表存储中。

创建知识存储后，可以了解如何使用[存储资源管理器](knowledge-store-view-storage-explorer.md)或 [Power BI](knowledge-store-connect-power-bi.md) 来访问该知识存储。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!TIP]
> 针对本文，建议阅读 [Postman 桌面应用](https://www.getpostman.com/)。 此外，本文中的[源代码](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)包含涵盖所有请求的 Postman 集合。 

## <a name="create-services-and-load-data"></a>创建服务并加载数据

本快速入门使用 Azure 认知搜索、Azure Blob 存储和用于 AI 的 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)。 

由于工作负荷很小，因此，在从 Azure 认知搜索调用认知服务时，认知服务在幕后会抽调一部分算力来免费处理事务（每天最多 20 个）。 只要你使用我们提供的示例数据，就可以跳过创建或附加认知服务资源的过程。

1. [下载 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 此数据是保存在某个 CSV 文件中的酒店评论数据（源自 Kaggle.com），其中包含客户对一家酒店的 19 条反馈。 

1. [创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，或在当前订阅下[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 你将使用 Azure 存储来保存要导入的原始内容，并使用知识存储（最终结果）。

   选择“StorageV2 (常规用途 V2)”帐户类型  。

1. 打开 Blob 服务页并创建一个名为 hotel-reviews 的容器  。

1. 单击“上载” 。 

    ![上传数据](media/knowledge-store-create-portal/upload-command-bar.png "上传酒店评论")

1. 选择在第一个步骤中下载的 **HotelReviews-Free.csv** 文件。

    ![创建 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "创建 Azure Blob 容器")

1. 对此资源的操作即将完成，但在退出这些页面之前，请使用左侧导航窗格中的链接打开“访问密钥”页。  获取用于从 Blob 存储检索数据的连接字符串。 连接字符串类似于以下示例：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. 仍然是在门户中，切换至 Azure 认知搜索。 [新建服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本练习中使用免费服务。

## <a name="configure-postman"></a>配置 Postman

安装并设置 Postman。

### <a name="download-and-install-postman"></a>下载并安装 Postman

1. 下载 [Postman 集合源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)。
1. 选择“文件” > “导入”将源代码导入 Postman。  
1. 选择“集合”选项卡，然后选择“...”（省略号）按钮。  
1. 选择“编辑”  。 
   
   ![显示导航栏的 Postman 应用](media/knowledge-store-create-rest/postman-edit-menu.png "转到 Postman 中的“编辑”菜单")
1. 在“编辑”对话框中，选择“变量”选项卡。   

在“变量”选项卡上，可以添加 Postman 每次在遇到双大括号中的值时要替换成的值。  例如，Postman 会将符号 `{{admin-key}}` 替换成为 `admin-key` 设置的当前值。 Postman 将在 URL、标头和请求正文等内容中进行这种替换。 

若要获取 `admin-key` 的值，请转到 Azure 认知搜索服务并选择“密钥”选项卡。  将 `search-service-name` 和 `storage-account-name` 更改为在[创建服务](#create-services-and-load-data)中选择的值。 使用存储帐户的“访问密钥”选项卡中的值设置 `storage-connection-string`。  其他值可保留默认设置。

![Postman 应用变量选项卡](media/knowledge-store-create-rest/postman-variables-window.png "Postman 的变量窗口")


| 变量    | 从何处获取 |
|-------------|-----------------|
| `admin-key` | 在 Azure 认知搜索服务的“密钥”页上。   |
| `api-version` | 保留为“2019-05-06-Preview”。  |
| `datasource-name` | 保留为“hotel-reviews-ds”。  | 
| `indexer-name` | 保留为“hotel-reviews-ixr”。  | 
| `index-name` | 保留为“hotel-reviews-ix”。  | 
| `search-service-name` | Azure 认知搜索服务的名称。 该 URL 为 `https://{{search-service-name}}.search.windows.net`。 | 
| `skillset-name` | 保留为“hotel-reviews-ss”。  | 
| `storage-account-name` | 存储帐户名称。 | 
| `storage-connection-string` | 在存储帐户中的“访问密钥”选项卡上，选择“密钥 1” > “连接字符串”。    | 
| `storage-container-name` | 保留为“hotel-reviews”。  | 

### <a name="review-the-request-collection-in-postman"></a>查看 Postman 中的请求集合

创建知识存储时，必须发出四个 HTTP 请求： 

- **创建索引的 PUT 请求**：此索引保存 Azure 认知搜索使用和返回的数据。
- **创建数据源的 POST 请求**：此数据源将 Azure 认知搜索行为连接到数据和知识存储的存储帐户。 
- **创建技能集的 PUT 请求**：技能集指定应用于知识存储的数据和结构的扩充。
- **创建索引器的 PUT 请求**：运行索引器可读取数据、应用技能集并存储结果。 必须在最后运行此请求。

[源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)包含这四个请求的 Postman 集合。 若要发出请求，请在 Postman 中选择请求对应的选项卡。 然后添加 `api-key` 和 `Content-Type` 请求标头。 将 `api-key` 的值设置为 `{{admin-key}}`。 将 `Content-type` 值设置为 `application/json`。 

![显示 Postman 中标头界面的屏幕截图](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> 必须在所有请求中设置 `api-key` 和 `Content-type` 标头。 如果 Postman 识别了某个变量，该变量将以橙色文本显示，如上面的屏幕截图中的 `{{admin-key}}` 所示。 如果变量拼写错误，它将以红色文本显示。
>

## <a name="create-an-azure-cognitive-search-index"></a>创建 Azure 认知搜索索引

创建 Azure 认知搜索索引来表示你要搜索、筛选并向其应用扩充的数据。 通过向 `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` 发出 PUT 请求来创建索引。 Postman 会将双大括号中的符号（例如 `{{search-service-name}}`、`{{index-name}}` 和 `{{api-version}}`）替换为[配置 Postman](#configure-postman) 中设置的值。 如果你使用其他工具发出 REST 命令，则必须自行替换这些变量。

在请求正文中设置 Azure 认知搜索索引的结构。 在 Postman 中设置 `api-key` 和 `Content-type` 标头后，转到请求的“正文”窗格。  应会看到以下 JSON。 如果未看到，请选择“Raw” > “JSON (application/JSON)”，然后粘贴以下代码作为正文：  

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

此索引定义是要向用户显示的数据（酒店名称、评论内容和日期）、搜索元数据和 AI 扩充数据（情绪、关键短语和语言）的组合。

选择“发送”以发出 PUT 请求。  应会看到状态 `201 - Created`。 如果看到其他状态，请在“正文”窗格中查找包含错误消息的 JSON 响应。  

## <a name="create-the-datasource"></a>创建数据源

接下来，将 Azure 认知搜索连接到 Blob 存储中存储的酒店数据。 若要创建数据源，请向 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` 发送 POST 请求。 必须根据前面所述设置 `api-key` 和 `Content-Type` 标头。 

在 Postman 中，转到“创建数据源”请求，然后转到“正文”窗格。   应会看到以下代码：

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

选择“发送”以发出 POST 请求。  

## <a name="create-the-skillset"></a>创建技能集 

下一步是指定技能集，用于指定要应用的扩充，以及要将结果存储到的知识存储。 在 Postman 中选择“创建技能集”选项卡。  此请求向 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` 发送 PUT。 根据前面所述设置 `api-key` 和 `Content-type` 标头。 

有两个大型顶级对象：`skills` 和 `knowledgeStore`。 `skills` 对象中的每个对象都是一个扩充服务。 每个扩充服务具有 `inputs` 和 `outputs`。 `LanguageDetectionSkill` 包含 `Language` 的输出 `targetName`。 此节点的值由其他大多数技能用作输入。 源为 `document/Language`。 在指定数据如何流入知识存储表的 `ShaperSkill` 中，使用一个节点的输出作为另一个节点的输入这一功能尢为明显。

`knowledge_store` 对象通过 `{{storage-connection-string}}` Postman 变量连接到存储帐户。 `knowledge_store` 包含增强的文档与知识存储中的表和列之间的一组映射。 

若要生成技能集，请在 Postman 中选择“发送”按钮来放置 (PUT) 请求： 

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>创建索引器

最后一步是创建索引器。 索引器读取数据并激活技能集。 在 Postman 中选择“创建索引器”请求，然后查看正文。  索引器的定义引用创建的其他多个资源：数据源、索引和技能集。 

`parameters/configuration` 对象控制索引器引入数据的方式。 在这种情况下，输入数据位于带有标题行和逗号分隔值的单个文档中。 文档键是文档的唯一标识符。 文档键在编码之前是源文档的 URL。 最后，技能集输出值（例如语言代码、情绪和关键短语）将映射到它们在文档中的位置。 尽管 `Language` 只有单个值，但 `Sentiment` 将应用到 `pages` 数组中的每个元素。 `Keyphrases` 是一个数组，也会应用到 `pages` 数组中的每个元素。

设置 `api-key` 和 `Content-type` 标头并确认请求正文类似于后面的源代码后，请在 Postman 中选择“发送”。  Postman 随即向 `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` 发送一个 PUT 请求。 Azure 认知搜索将创建并运行索引器。 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>运行索引器 

在 Azure 门户中，转到 Azure 认知搜索服务的“概览”页。  依次选择“索引器”选项卡、“hotels-reviews-ixr”。   如果索引器尚未运行，请选择“运行”。  索引任务可能会引发一些与语言识别相关的警告。 数据中的某些评论是以认知技能尚不支持的语言编写的。 

## <a name="next-steps"></a>后续步骤

使用认知服务扩充数据并将结果投影到知识存储后，接下来可以使用存储资源管理器或 Power BI 来浏览扩充的数据集。

若要了解如何使用存储资源管理器浏览此知识存储，请参阅以下演练：

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)

若要了解如何将此知识存储连接到 Power BI，请参阅以下演练：

> [!div class="nextstepaction"]
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)

若要重复此练习或尝试其他 AI 扩充演练，请删除 **hotel-reviews-idxr** 索引器。 删除该索引器会将每日的免费事务计数器重置为零。
