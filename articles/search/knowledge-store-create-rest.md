---
title: 使用 REST 创建知识存储 - Azure 搜索
description: 使用 REST API 和 Postman 创建 Azure 搜索知识存储，用于保存认知搜索管道的扩充内容。
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: 26dc66474eecffd7f5a34bcfcaf93fd49f59606c
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936501"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>使用 REST 创建 Azure 搜索知识存储

知识存储是 Azure 搜索中的一项功能，它可以保留 AI 扩充管道的输出供后续分析或进行其他下游处理。 AI 扩充的管道接受图像文件或非结构化文本文件，使用 Azure 搜索为其编制索引，应用认知服务中的 AI 扩充（例如图像分析和自然语言处理），并将结果保存到 Azure 存储中的知识存储。 然后，可以使用 Power BI 或存储资源管理器等工具来浏览知识存储。

在本文中，你将使用 REST API 接口基于一系列酒店评论引入、编制索引和应用 AI 扩充。 酒店评论已导入到 Azure Blob 存储，结果已作为知识存储保存在 Azure 表存储中。

创建知识存储后，可以了解如何使用[存储资源管理器](knowledge-store-view-storage-explorer.md)或 [Power BI](knowledge-store-connect-power-bi.md) 来访问此知识存储。

## <a name="1---create-services"></a>1 - 创建服务

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本教程中使用免费服务。

+ [创建一个 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)用于存储示例数据和知识存储。 存储帐户必须使用与 Azure 搜索服务相同的位置（例如“美国西部”）。 “帐户类型”必须是“StorageV2 (常规用途 V2)”（默认设置）或“Storage (常规用途 V1)”。   

+ 建议：使用 [Postman 桌面应用](https://www.getpostman.com/)，将请求发送到 Azure 搜索。 可将 REST API 与任何能够处理 HTTP 请求和响应的工具配合使用。 Postman 非常适合用于探索 REST API，本文将使用此工具。 此外，本文中的[源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)包含 Postman 请求集合。 

## <a name="2---store-the-data"></a>2 - 存储数据

将酒店评论 CSV 文件载入 Azure Blob 存储，使之可由 Azure 搜索索引器访问，并可通过 AI 扩充管道进行馈送。

### <a name="create-an-azure-blob-container-with-the-data"></a>创建包含数据的 Azure Blob 容器

1. [下载已保存到 CSV 文件中的酒店评论数据 (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 此数据来源于 Kaggle.com，包含客户对酒店的反馈。
1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户。
1. [创建 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 若要创建容器，请在存储帐户的左侧导航栏中单击“Blob”，然后单击命令栏上的“+ 容器”。  
1. 请输入 `hotel-reviews` 作为新容器的**名称**。
1. 选择任何“公共访问级别”。  我们使用了默认值。
1. 单击“确定”创建 Azure Blob 容器。 
1. 打开新的 `hotels-review` 容器，单击“上传”，然后选择在第一个步骤中下载的“HotelReviews-Free.csv”文件。  

    ![上传数据](media/knowledge-store-create-portal/upload-command-bar.png "上传酒店评论")

1. 单击“上传”，将该 CSV 文件导入 Azure Blob 存储。  随后会显示新容器。

    ![创建 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "创建 Azure Blob 容器")

## <a name="3---configure-postman"></a>3 - 配置 Postman

下载 [Postman 集合源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)，并使用“文件”>“导入...”将其导入 Postman。  切换到“集合”选项卡，单击“...”按钮并选择“编辑”。    

![显示导航栏的 Postman 应用](media/knowledge-store-create-rest/postman-edit-menu.png "导航到 Postman 中的“编辑”菜单")

在出现的“编辑”对话框中，导航到“变量”选项卡。  

在“变量”选项卡中，可以添加 Postman 每次在遇到双大括号中的值时要替换成的值。  例如，Postman 会将符号 `{{admin-key}}` 替换为 `admin-key` 的“当前值”。 Postman 将在 URL、标头和请求正文等内容中进行这种替换。 

在搜索服务的“密钥”选项卡中可以找到 `admin-key` 的值。  需将 `search-service-name` 和 `storage-account-name` 更改为在[步骤 1](#1---create-services) 中选择的值。 根据存储帐户的“访问密钥”选项卡中的值设置 `storage-connection-string`。  其他值可保持不变。

![Postman 应用变量选项卡](media/knowledge-store-create-rest/postman-variables-window.png "Postman 的变量窗口")


| 变量    | 从何处获取 |
|-------------|-----------------|
| `admin-key` | 搜索服务的“密钥”选项卡               |
| `api-version` | 保留为“2019-05-06-Preview” |
| `datasource-name` | 保留为“hotel-reviews-ds” | 
| `indexer-name` | 保留为“hotel-reviews-ixr” | 
| `index-name` | 保留为“hotel-reviews-ix” | 
| `search-service-name` | 搜索服务的主名称。 URL 为 `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | 保留为“hotel-reviews-ss” | 
| `storage-account-name` | 存储帐户的主名称 | 
| `storage-connection-string` | 存储帐户的“访问密钥”选项卡、“密钥 1”、“连接字符串”    | 
| `storage-container-name` | 保留为“hotel-reviews” | 

### <a name="review-the-request-collection-in-postman"></a>查看 Postman 中的请求集合

创建知识存储需要发出四个 HTTP 请求： 

1. 创建索引的 PUT 请求。 此索引保存 Azure 搜索使用和返回的数据。
1. 创建数据源的 POST 请求。 此数据源将 Azure 搜索行为连接到数据和知识存储的存储帐户。 
1. 创建技能集的 PUT 请求。 技能集指定应用于知识存储的数据和结构的扩充。
1. 创建索引器的 PUT 请求。 运行索引器可读取数据、应用技能集并存储结果。 必须在最后运行此请求。

[源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)包含包含这四个请求的 Postman 集合。 若要发出请求，请切换到 Postman 中的“请求”选项卡，并添加 `api-key` 和 `Content-Type` 请求标头。 将 `api-key` 的值设置为 `{{admin-key}}`。 将 `Content-type` 值设置为 `application/json`。 

> [!div class="mx-imgBorder"]
> ![显示 Postman 中标头界面的屏幕截图](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> 需要在所有请求中设置 `api-key` 和 `Content-type` 标头。 如果 Postman 识别了某个变量，该变量将以橙色文本显示，如屏幕截图中的 `{{admin-key}}` 所示。 如果变量拼写错误，它将以红色文本显示。
>

## <a name="4---create-an-azure-search-index"></a>4 - 创建 Azure 搜索索引

需要创建 Azure 搜索索引来表示执行搜索、筛选和扩充所依据的数据。 通过向 `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` 发出 PUT 请求来创建索引。 Postman 会将双大括号中的符号（例如 `{{search-service-name}}`、`{{index-name}}` 和 `{{api-version}}`）替换为[步骤 3](#3---configure-postman) 中指定的值。 如果使用其他工具发出 REST 命令，则必须自行替换这些变量。

在请求正文中指定 Azure 搜索索引的结构。 在 Postman 中设置 `api-key` 和 `Content-type` 标头后，切换到请求的“正文”窗格。  应会看到以下 JSON，如果未看到，请选择“Raw”和“JSON (application/JSON)”并粘贴以下代码作为正文：  

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

你将看到，此索引定义是要向用户显示的数据（酒店名称、评论内容、日期等）、搜索元数据和 AI 扩充数据（情绪、关键短语和语言）的组合。

按“发送”按钮发出 PUT 请求。  应会收到状态消息 `201 - Created`。 如果收到其他状态，“正文”窗格将显示 JSON 响应和错误消息。  

## <a name="5---create-the-datasource"></a>5 - 创建数据源

现在，需要将 Azure 搜索连接到[步骤 2](#2---store-the-data) 中存储的酒店数据。 通过向 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` 发出 POST 请求来创建数据源。 同样，需要根据前面所述设置 `api-key` 和 `Content-Type` 标头。 

在 Postman 中，打开“创建数据源”请求。 切换到“正文”窗格，其中应包含以下代码： 

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

按“发送”按钮发出 POST 请求。  

## <a name="6---create-the-skillset"></a>6 - 创建技能集 

下一步是指定技能集，用于指定要应用的扩充，以及要将结果存储到的知识存储。 在 Postman 中，打开“创建技能集”选项卡。此请求向 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` 发送 PUT。
根据前面所述设置 `api-key` 和 `Content-type` 标头。 

有两个大型顶级对象：`"skills"` 和 `"knowledgeStore"`。 `"skills"` 对象中的每个对象都是一个扩充服务。 每个扩充服务具有 `"inputs"` 和 `"outputs"`。 请注意 `LanguageDetectionSkill` 包含 `"Language"` 的输出 `targetName`。 此节点的值由其他大多数技能用作输入，源为 `document/Language`。 在指定数据如何流入知识存储表的 `ShaperSkill` 中，使用一个节点的输出作为另一个节点的输入这一功能尢为明显。

`"knowledge_store"` 对象通过 `{{storage-connection-string}}` Postman 变量连接到存储帐户。 然后，它包含增强的文档与知识存储本身中提供的表和列之间的一组映射。 

若要生成技能集，请在 Postman 中按“发送”按钮来放置 (PUT) 请求。 

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

## <a name="7---create-the-indexer"></a>7 - 创建索引器

最后一步是创建索引器，用于实际读取数据并激活技能集。 在 Postman 中，切换到“创建索引器”请求并查看正文。 可以看到，索引器的定义引用了创建的其他多个资源 - 数据源、索引和技能集。 

`"parameters/configuration"` 对象控制索引器引入数据的方式。 在这种情况下，输入数据位于带有标题行和逗号分隔值的单个文档中。 文档键是文档的唯一标识符，在编码之前是源文档的 URL。 最后，技能集输出值（例如语言代码、情绪和关键短语）将映射到它们在文档中的相应位置。 请注意，尽管 `Language` 只有单个值，但 `Sentiment` 将应用到 `pages` 数组中的每个元素。 `Keyphrases` 本身是一个数组，也会应用到 `pages` 数组中的每个元素。

设置 `api-key` 和 `Content-type` 标头并确认请求正文类似于后面的源代码后，请在 Postman 中按“发送”。  Postman 会将请求放置到 `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`。 Azure 搜索将创建并运行索引器。 

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

## <a name="8---run-the-indexer"></a>8 - 运行索引器 

在 Azure 门户中，导航到搜索服务的“概述”并选择“索引器”选项卡。   单击在上一步骤中创建的“hotels-reviews-ixr”。  如果索引器尚未运行，请按“运行”按钮。  索引任务可能会引发一些与语言识别相关的警告，因为数据中的某些评论是以认知技能尚不支持的语言编写的。 

## <a name="next-steps"></a>后续步骤

使用认知服务扩充数据并将结果投影到知识存储后，接下来可以使用存储资源管理器或 Power BI 来浏览扩充的数据集。

若要了解如何使用存储资源管理器浏览此知识存储，请参阅以下演练。

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)

若要了解如何将此知识存储连接到 Power BI，请参阅以下演练。

> [!div class="nextstepaction"]
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)

若要重复此练习或尝试其他 AI 扩充演练，请删除 *hotel-reviews-idxr* 索引器。 删除该索引器会将每日的免费事务计数器重置为零。
