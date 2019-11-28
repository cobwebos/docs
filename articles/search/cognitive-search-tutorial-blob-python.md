---
title: 教程：使用 REST API 在 Python 中创建技能组
titleSuffix: Azure Cognitive Search
description: 本教程通过一个示例分步说明如何使用 Jupyter Python 笔记本在 Azure 认知搜索中完成数据提取、自然语言和图像 AI 处理。 提取的数据会编制索引并通过查询轻松访问。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d7b4755bb2e69c4a852901b71d917c6baa5d63ae
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406414"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>教程：使用 REST 和 Python 创建 AI 扩充管道

本教程介绍使用认知技能在 Azure 认知搜索中扩充编程数据的机制。  技能由自然语言处理 (NLP) 和认知服务中的图像分析功能提供支持。 通过技能集组合和配置，可以提取图像或扫描的文档文件的文本和文本表示形式。 还可以检测语言、实体、关键短语等。 最终结果是搜索索引中有丰富附加内容（使用索引管道中的 AI 扩充创建）。 

在本教程中，你将使用 Python 执行以下任务：

> [!div class="checklist"]
> * 创建一个索引管道，用于扩充索引路由中的示例数据
> * 应用内置技能：实体识别、语言检测、文本处理、关键短语提取
> * 了解如何通过在技能集中将输入映射到输出来链接技能
> * 执行请求并查看结果
> * 重置索引和索引器以进一步开发

输出是 Azure 认知搜索中的全文搜索索引。 可以使用[同义词](search-synonyms.md)、[评分配置文件](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[分析器](search-analyzers.md)和[筛选器](search-filters.md)等其他标准功能来增强索引。 

本教程在免费服务中运行，但免费事务数目限制为每日 20 个文档。 若要在同一天多次运行本教程，请使用可以运行更多次的较小文件集。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API 以及在 Azure 认知搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 认知搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程使用了以下服务、工具和数据。 

+ [创建一个 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，用于存储示例数据。 确保存储帐户与 Azure 认知搜索位于同一区域。

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)，提供 Python 3.x 和 Jupyter Notebook。

+ [示例数据](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)包括不同类型的小型文件集。 

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本教程中使用免费服务。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

必须有 Azure 认知搜索服务 URL 和访问密钥，才能与此服务交互。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="prepare-sample-data"></a>准备示例数据

扩充管道从 Azure 数据源提取数据。 源数据必须源自受支持的 [Azure 认知搜索索引器](search-indexer-overview.md)数据源类型。 本演练使用 Blob 存储来展示多种内容类型。

1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户，单击“Blob”，然后单击“+ 容器”   。

1. [创建一个 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)用于包含示例数据。 可将“公共访问级别”设为任何有效值。

1. 创建容器后，请将其打开，在命令栏上选择“上传”，以上传在上一步骤中下载的示例文件。 

   ![Azure Blob 存储中的源文件](./media/cognitive-search-quickstart-blob/sample-data.png)

1. 加载示例文件后，获取 Blob 存储的容器名称和连接字符串。 为此，请在 Azure 门户中导航到你的存储帐户。 单击“访问密钥”，然后复制“连接字符串”字段值   。

连接字符串的格式如下：`DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

将连接字符串放在方便的位置。 下一步会用到。

可通过其他方式指定连接字符串，例如，提供共享访问签名。 若要详细了解数据源凭据，请参阅[为 Azure Blob 存储编制索引](search-howto-indexing-azure-blob-storage.md#Credentials)。

## <a name="create-a-jupyter-notebook"></a>创建 Jupyter 笔记本

> [!Note]
> 本文介绍如何使用一系列 Python 脚本构建数据源、索引、索引器和技能组。 要下载完整的笔记本示例，请转到[Azure-Search-python-samples 存储库](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook)。

使用 Anaconda 导航器启动 Jupyter Notebook，并创建新的 Python 3 笔记本。

## <a name="connect-to-azure-cognitive-search"></a>连接到 Azure 认知搜索

在笔记本中，运行此脚本以加载用于处理 JSON 和构建 HTTP 请求的库。

```python
import json
import requests
from pprint import pprint
```

接下来，定义数据源、索引、索引器和技能集的名称。 运行此脚本以设置本教程中的名称。

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> 在免费服务上，只能设置三个索引、索引器和数据源。 本教程每样创建一个。 在继续操作之前，请确保有足够的空间来创建新对象。

在以下脚本中，替换搜索服务 (YOUR-SEARCH-SERVICE-NAME) 和管理员 API 密钥 (YOUR-ADMIN-API-KEY) 的占位符，然后运行脚本以设置搜索服务终结点。

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>创建数据源

准备好服务和源文件后，开始汇编索引管道的组件。 首先创建一个数据源对象，告知 Azure 认知搜索如何检索外部源数据。

在以下脚本中，将占位符 YOUR-BLOB-RESOURCE-CONNECTION-STRING 替换为上一步中创建的 blob 的连接字符串。 然后，运行该脚本以创建一个名为 `cogsrch-py-datasource` 的数据源。

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

请求应返回状态代码 201，确认成功。

在 Azure 门户中，在搜索服务仪表板页面上，验证 cogsrch-py-datasource 是否出现在“数据源”列表中  。 单击“刷新”更新页面  。

![门户中的“数据源”磁贴](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "门户中的“数据源”磁贴")

## <a name="create-a-skillset"></a>创建技能集

在此步骤中，你将定义一组要应用到数据的扩充步骤。 每个扩充步骤称为“技能”，一组扩充步骤称为“技能集”。   本教程对技能集使用以下[内置认知技能](cognitive-search-predefined-skills.md)：

+ [语言检测](cognitive-search-skill-language-detection.md)：识别内容的语言。

+ [文本拆分](cognitive-search-skill-textsplit.md)：将大段内容拆分为较小区块，然后调用关键短语提取技能。 关键短语提取接受不超过 50,000 个字符的输入。 有几个示例文件需要拆分才能保留在此限制范围内。

+ [实体识别](cognitive-search-skill-entity-recognition.md)：从 Blob 容器中的内容提取组织名称。

+ [关键短语提取](cognitive-search-skill-keyphrases.md)：取出最关键的短语。 

### <a name="python-script"></a>Python 脚本
运行以下脚本以创建名为 `cogsrch-py-skillset` 的技能组。

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

请求应返回状态代码 201，确认成功。

将对每个页面应用关键短语提取技能。 将上下文设置为 `"document/pages/*"`，对文档/页面数组的每个成员运行此扩充器（适用于文档中的每个页面）。

每个技能会针对文档的内容执行。 在处理期间，Azure 认知搜索会解码每个文档，以从不同的文件格式读取内容。 从源文件中找到的文本将放入 `content` 字段（每个文档对应一个字段）。 因此，请将输入设置为 `"/document/content"`。

技能集的图形表示形式如下所示。

![了解技能组](media/cognitive-search-tutorial-blob/skillset.png "了解技能组")

输出可以映射到索引、用作下游技能的输入，或者既映射到索引又用作输入（在语言代码中就是这样）。 在索引中，语言代码可用于筛选。 文本分析技能使用语言代码作为输入来告知有关断字的语言规则。

若要详细了解技能集的基础知识，请参阅[如何定义技能集](cognitive-search-defining-skillset.md)。

## <a name="create-an-index"></a>创建索引

在本部分，你将通过指定要包含到可搜索索引中的字段并为每个字段设置搜索属性来定义索引架构。 字段具有某种类型，并可以采用特性来确定字段的使用方式（可搜索、可排序，等等）。 索引中的字段名称不一定要与源中的字段名称完全匹配。 在稍后的步骤中，我们将在索引器中添加字段映射以连接源-目标字段。 针对此步骤，请使用搜索应用程序相关的字段命名约定来定义索引。

本演练使用以下字段和字段类型：

| field-names: | id         | 内容   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

运行此脚本以创建名为 `cogsrch-py-index` 索引。

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

请求应返回状态代码 201，确认成功。

若要详细了解如何定义索引，请参阅[创建索引（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)。

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>创建索引器，映射字段，并执行转换

到目前为止，你已创建数据源、技能组和索引。 这三个组件属于某个[索引器](search-indexer-overview.md)，该索引器将每个片段一同提取到单个多阶段操作。 要在索引器中将这些对象捆绑在一起，必须定义字段映射。

+ 先处理 fieldMapping，再处理技能集；将数据源中的源字段映射到索引中的目标字段。 如果两端的字段名称和类型相同，则无需映射。

+ 先处理技能集，再处理 outputFieldMapping；引用不存在的 sourceFieldName，直到文档破解或扩充功能创建了它们。 targetFieldName 是索引中的字段。

除了将输入挂接到输出以外，还可以使用字段映射来平展数据结构。 有关详细信息，请参阅[如何将扩充字段映射到可搜索索引](cognitive-search-output-field-mapping.md)。

运行此脚本以创建名为 `cogsrch-py-indexer` 索引器。

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

请求应快速返回状态代码 201，但是，处理可能需要几分钟才能完成。 尽管数据集很小，但分析技能（如图像分析）属于计算密集型任务，需要时间。

使用下一部分的[检查索引器状态](#check-indexer-status)脚本来确定索引器进程何时完成。

> [!TIP]
> 创建索引器会调用管道。 如果访问数据、映射输入和输出或操作顺序出现问题，此阶段会显示这些问题。 要结合代码或脚本更改重新运行管道，可能需要先删除对象。 有关详细信息，请参阅[重置并重新运行](#reset)。

#### <a name="explore-the-request-body"></a>浏览请求正文

脚本将 `"maxFailedItems"` 设置为 -1，指示索引引擎在数据导入期间忽略错误。 此设置非常有用，因为演示数据源中的文档很少。 对于更大的数据源，请将值设置为大于 0。

另请注意配置参数中的 `"dataToExtract":"contentAndMetadata"` 语句。 该语句告知索引器从不同的文件格式以及与每个文件相关的元数据中提取内容。

提取内容后，可以设置 `imageAction`，以从数据源中的图像提取文本。 `"imageAction":"generateNormalizedImages"` 配置与 OCR 技能和文本合并技能相结合，告知索引器从图像中提取文本（例如，禁行交通标志中的单词“stop”），并将其嵌入到内容字段中。 此行为将应用到文档中嵌入的图像（例如 PDF 中的图像），以及数据源（例如 JPG 文件）中的图像。

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>检查索引器状态

定义索引器后，提交请求时会自动运行索引器。 根据定义的认知技能，索引编制花费的时间可能会超出预期。 要了解索引器处理是否已完成，请运行以下脚本。

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

在响应中，监视 lastResult 的 status 和 endTime 值。 定期运行脚本以检查状态。 索引器完成后，状态将设置为 success ，将指定 endTime，响应将包括在扩充期间发生的任何错误和警告。

![已创建索引器](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "已创建索引器")

处理某些源文件和技能的组合时经常会出现警告，这并不总是意味着出现了问题。 在本教程中，警告是善意的。 例如，其中一个没有文字的 JPEG 文件将在此屏幕截图中显示警告。

![索引器警告示例](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "索引器警告示例")

## <a name="query-your-index"></a>查询索引

索引编制完成后，运行可返回各个字段的内容的查询。 默认情况下，Azure 认知搜索返回前 50 条结果。 由于样本数据较小，因此使用默认设置即可正常操作。 但是，在处理较大的数据集时，可能需要在查询字符串中包含参数来返回更多结果。 有关说明，请参阅[如何将 Azure 认知搜索中的结果分页](search-pagination-page-layout.md)。

作为验证步骤，请查询所有字段的索引。

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

最终结果如以下示例所示。 屏幕截图仅显示响应的一部分。

![查询所有字段的索引](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "查询所有字段的索引")

输出是索引架构，其中包含每个字段的名称、类型和特性。

针对 `"*"` 提交另一个查询，以返回单个字段的所有内容，例如 `organizations`。

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

最终结果如以下示例所示。 屏幕截图仅显示响应的一部分。

![查询组织内容的索引](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "查询索引以返回组织内容")

针对本练习中的其他字段（content、languageCode、keyPhrases 和 organizations）重复上述步骤。 可以使用逗号分隔列表通过 `$select` 返回多个字段。

可以根据查询字符串的复杂性和长度，使用 GET 或 POST。 有关详细信息，请参阅[使用 REST API 进行查询](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重置并重新运行

在管道开发的前期试验阶段，设计迭代的最实用方法是删除 Azure 认知搜索中的对象，并允许代码重新生成这些对象。 资源名称是唯一的。 删除某个对象后，可以使用相同的名称重新创建它。

使用新定义为文档重新编制索引：

1. 删除索引以删除持久保存的数据。 删除索引器，以在服务中重新创建它。
2. 修改技能组和索引定义。
3. 在服务中重新创建索引和索引器，以运行管道。

可以使用门户删除索引、索引器和技能组。 删除索引器时，可以根据需要选择同时删除索引、技能组和数据源。

![删除搜索对象](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "在门户中删除搜索对象")

还可以使用脚本删除它们。 以下脚本将删除我们创建的技能组。 你可以轻松修改请求，以删除索引、索引器和数据源。

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

随着代码的成熟，可能需要优化重新生成策略。 有关详细信息，请参阅[如何重新生成索引](search-howto-reindex.md)。

## <a name="takeaways"></a>要点

本教程演示了通过创建组件部件（数据源、技能集、索引和索引器）生成扩充索引管道的基本步骤。

其中介绍了[内置技能组](cognitive-search-predefined-skills.md)、技能组定义，以及通过输入和输出将技能链接在一起的方式。 此外，还提到需要使用索引器定义中的 `outputFieldMappings`，将管道中的扩充值路由到 Azure 认知搜索服务中的可搜索索引。

最后，介绍了如何测试结果并重置系统以进一步迭代。 本教程提到，针对索引发出查询会返回扩充的索引管道创建的输出。 在此版本中，可以通过某个机制来查看内部构造（系统创建的扩充文档）。 此外，本教程还介绍了如何检查索引器状态，以及在重新运行管道之前必须删除的对象。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，最快的清理方式是删除包含 Azure 认知搜索服务和 Azure Blob 服务的资源组。 假设你已将这两个服务放在同一个组中，删除该资源组会永久删除其中的所有内容，包括在本教程中创建的服务和任何存储内容。 在门户中，资源组名称显示在每个服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

使用自定义技能自定义或扩展管道。 创建自定义技能并将其添加到技能集可以载入自己编写的文本或图像分析代码。

> [!div class="nextstepaction"]
> 示例：[创建 AI 扩充的自定义技能](cognitive-search-create-custom-skill-example.md)
