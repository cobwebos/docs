---
title: REST 教程：使用认知搜索生成 AI 扩充管道 - Azure 搜索
description: 通过一个示例来逐步了解如何使用 Postman 和 Azure 搜索 REST API 对 JSON Blob 中内容进行文本提取和自然语言处理。
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 726fdd6aeebac970142fa9225381af77114bfe42
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274116"
---
# <a name="tutorial-add-structure-to-unstructured-content-with-cognitive-search"></a>教程：使用认知搜索将结构添加到“非结构化内容”

如果你在使用非结构化文本或图像内容，Azure 搜索的[认知搜索](cognitive-search-concept-intro.md)功能可帮助你提取信息，并创建可用于全文搜索或知识挖掘方案的新内容。 尽管认知搜索可以处理图像文件（JPG、PNG、TIFF），但本教程侧重于基于文字的内容，介绍如何应用语言检测和文本分析来创建可在查询、分面和筛选器中利用的新字段和信息。

> [!div class="checklist"]
> * 从整个文档（非结构化文本，例如 Azure Blob 存储中的 PDF、MD、DOCX 和 PPTX）着手。
> * 定义一个管道，用于提取文本、检测语言、识别实体和检测关键短语。
> * 定义用于存储输出（原始内容，加上管道生成的名称/值对）的索引。
> * 执行管道以开始转换和分析，以及创建和加载索引。
> * 使用全文搜索和丰富的查询语法浏览结果。

需要创建多个服务才能完成本演练，此外还需要安装 [Postman 桌面应用](https://www.getpostman.com/)或其他 Web 测试工具来发出 REST API 调用。 

如果你没有 Azure 订阅，请在开始之前建立一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="download-files"></a>下载文件

1. 打开此 [OneDrive 文件夹](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)，然后单击左上角的“下载”将文件复制到计算机。  

1. 右键单击 zip 文件并选择“全部提取”。  有 14 个不同类型的文件。 本练习将使用其中的 7 个文件。

## <a name="1---create-services"></a>1 - 创建服务

本演练使用 Azure 搜索编制索引和进行查询、使用认知服务进行 AI 扩充，并使用 Azure Blob 存储提供数据。 如果可能，请在同一区域和资源组中创建上述所有三个服务，使它们相互靠近并易于管理。 在实践中，Azure 存储帐户可位于任意区域。

### <a name="start-with-azure-storage"></a>从 Azure 存储开始

1. [登录到 Azure 门户](https://portal.azure.com/)并单击“+ 创建资源”。 

1. 搜索“存储帐户”，并选择“Microsoft 的存储帐户”产品/服务。 

   ![创建存储帐户](media/cognitive-search-tutorial-blob/storage-account.png "创建存储帐户")

1. 在“基本信息”选项卡中，必须填写以下项。 对于其他任何字段，请接受默认设置。

   + 资源组  。 选择现有的资源组或创建新资源组，但对于所有服务请使用相同的组，以便可以统一管理这些服务。

   + **存储帐户名称**。 如果你认为将来可能会用到相同类型的多个资源，请使用名称来区分类型和区域，例如 *blobstoragewestus*。 

   + **位置**。 如果可能，请选择 Azure 搜索和认知服务所用的相同位置。 使用一个位置可以避免带宽费用。

   + **帐户类型**。 选择默认设置“StorageV2 (常规用途 v2)”  。

1. 单击“查看 + 创建”以创建服务。 

1. 创建后，单击“转到资源”打开“概述”页。 

1. 单击“Blob”服务。 

1. 单击“+ 容器”创建容器，并将其命名为 *cog-search-demo*。 

1. 选择“cog-search-demo”，然后单击“上传”打开下载文件所保存到的文件夹。   选择所有的非图像文件。 应有 7 个文件。 单击“确定”以上传。 

   ![上传示例文件](media/cognitive-search-tutorial-blob/sample-files.png "上传示例文件")

1. 在退出 Azure 存储之前获取一个连接字符串，以便可以在 Azure 搜索中构建连接。 

   1. 向后浏览到存储帐户的“概述”页（我们使用了 *blobstragewestus* 作为示例）。 
   
   1. 在左侧导航窗格中，选择“访问密钥”并复制其中一个连接字符串。  

   连接字符串是类似于以下示例的 URL：

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. 将连接字符串保存到记事本中。 稍后在设置数据源连接时需要用到它。

### <a name="cognitive-services"></a>认知服务

认知搜索中的 AI 扩充由认知服务（包括用于自然语言和图像处理的文本分析与计算机视觉）提供支持。 如果你的目标是完成实际原型或项目，则此时应预配认知服务（在 Azure 搜索所在的同一区域中），以便可将认知服务附加到索引操作。

但是，对于本练习，可以跳过资源预配，因为 Azure 搜索在幕后可以连接到认知服务，并为每个索引器运行提供 20 个免费事务。 由于本教程使用 7 个事务，因此免费的分配已足够。 对于大型项目，请计划在即用即付 S0 层预配认知服务。 有关详细信息，请参阅[附加认知服务](cognitive-search-attach-cognitive-services.md)。

### <a name="azure-search"></a>Azure 搜索

第三个组件是可以[在门户中创建](search-create-service-portal.md)的 Azure 搜索。 可使用免费层完成本演练。 

与处理 Azure Blob 存储时一样，请花片刻时间来收集访问密钥。 此外，在开始构建请求时，需要提供终结点和管理 API 密钥用于对每个请求进行身份验证。

### <a name="get-an-admin-api-key-and-url-for-azure-search"></a>获取 Azure 搜索的管理 API 密钥和 URL

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取搜索服务的名称。  可以通过查看终结点 URL 来确认服务名称。 如果终结点 URL 为 `https://mydemo.search.windows.net`，则服务名称为 `mydemo`。

2. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

    此外，获取查询密钥。 最好使用只读权限发出查询请求。

![获取服务名称以及管理密钥和查询密钥](media/search-get-started-nodejs/service-name-and-keys.png)

所有请求要求在发送到服务的每个请求的标头中指定 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="2---set-up-postman"></a>2 - 设置 Postman

启动 Postman 并设置 HTTP 请求。 如果不熟悉此工具，请参阅[使用 Postman 探索 Azure 搜索 REST API](search-get-started-postman.md) 了解详细信息。

本教程中使用的请求方法是 **POST**、**PUT** 和 **GET**。 你将使用这些方法对搜索服务发出四个 API 调用：创建数据源、创建技能集、创建索引和创建索引器。

在标头中，将“Content-type”设置为 `application/json`，将 `api-key` 设置为 Azure 搜索服务的管理 API 密钥。 设置标头后，可将其用于本练习中的每个请求。

  ![Postman 请求 URL 和标头](media/search-get-started-postman/postman-url.png "Postman 请求 URL 和标头")

## <a name="3---create-the-pipeline"></a>3 - 创建管道

在 Azure 搜索中，AI 处理是在索引编制（或数据引入）期间发生的。 本演练部分将创建四个对象：数据源、索引定义、技能集和索引器。 

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

[数据源对象](https://docs.microsoft.com/rest/api/searchservice/create-data-source)为包含文件的 Blob 容器提供连接字符串。

1. 使用 **POST** 和以下 URL（请将 YOUR-SERVICE-NAME 替换为实际的服务名称）。

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. 在请求的**正文**中复制以下 JSON 定义（请将 `connectionString` 替换为存储帐户的实际连接）。 

   此外，请记得编辑容器名称。 在前一步骤中，我们已建议使用“cog-search-demo”作为容器名称。

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. 发送请求。 应会看到状态代码 201（确认成功）。 

如果收到 403 或 404 错误，请检查请求构造：`api-version=2019-05-06` 应位于终结点上，`api-key` 应位于标头中的 `Content-Type` 后面，并且其值必须对搜索服务有效。 可以通过联机 JSON 验证程序运行 JSON 文档，以确保语法正确。 

### <a name="step-2-create-a-skillset"></a>步骤 2：创建技能集

[技能集对象](https://docs.microsoft.com/rest/api/searchservice/create-skillset)是应用到内容的一组扩充步骤。 

1. 使用 **PUT** 和以下 URL（请将 YOUR-SERVICE-NAME 替换为实际的服务名称）。

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. 在请求的**正文**中复制以下 JSON 定义。 此技能集包括以下内置技能。

   | 技能                 | 说明    |
   |-----------------------|----------------|
   | [实体识别](cognitive-search-skill-entity-recognition.md) | 从 Blob 容器中的内容提取人员、组织和位置的名称。 |
   | [语言检测](cognitive-search-skill-language-detection.md) | 检测内容的语言。 |
   | [文本拆分](cognitive-search-skill-textsplit.md)  | 将大段内容拆分为较小区块，然后调用关键短语提取技能。 关键短语提取接受不超过 50,000 个字符的输入。 有几个示例文件需要拆分才能保留在此限制范围内。 |
   | [关键短语提取](cognitive-search-skill-keyphrases.md) | 提取出最相关的关键短语。 |

   每个技能会针对文档的内容执行。 在处理期间，Azure 搜索会解码每个文档，以从不同的文件格式读取内容。 从源文件中找到的文本将放入一个生成的 ```content``` 字段（每个文档对应一个字段）。 因此，输入将变为 ```"/document/content"```。

   对于关键短语提取，由于我们使用了文本拆分器技能将较大文件分解成多个页面，因此关键短语提取技能的上下文是 ```"document/pages/*"```（适用于文档中的每个页面），而不是 ```"/document/content"```。

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    技能集的图形表示形式如下所示。 

    ![了解技能集](media/cognitive-search-tutorial-blob/skillset.png "了解技能集")

1. 发送请求。 Postman 应返回状态代码 201（确认成功）。 

> [!NOTE]
> 输出可以映射到索引、用作下游技能的输入，或者既映射到索引又用作输入（在语言代码中就是这样）。 在索引中，语言代码可用于筛选。 文本分析技能使用语言代码作为输入来告知有关断字的语言规则。 若要详细了解技能集的基础知识，请参阅[如何定义技能集](cognitive-search-defining-skillset.md)。

### <a name="step-3-create-an-index"></a>步骤 3：创建索引

[索引](https://docs.microsoft.com/rest/api/searchservice/create-index)提供所需的架构用于在反向索引中创建内容的实际表达形式，以及在 Azure 搜索中创建其他构造。 索引的最大组件是字段集合，其中的数据类型和属性确定了 Azure 搜索中的内容和行为。

1. 使用 **PUT** 和以下 URL（请将 YOUR-SERVICE-NAME 替换为实际的服务名称）来命名索引。

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. 在请求的**正文**中复制以下 JSON 定义。 `content` 字段存储文档本身。 `languageCode`、`keyPhrases` 和 `organizations` 的附加字段表示技能集创建的新信息（字段和值）。

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. 发送请求。 Postman 应返回状态代码 201（确认成功）。 

### <a name="step-4-create-and-run-an-indexer"></a>步骤 4：创建并运行索引器

[索引器](https://docs.microsoft.com/rest/api/searchservice/create-indexer)驱动管道。 到目前为止创建的三个组件（数据源、技能集、索引）是索引器的输入。 在 Azure 搜索中创建索引器是运转整个管道的事件。 

1. 使用 **PUT** 和以下 URL（请将 YOUR-SERVICE-NAME 替换为实际的服务名称）来命名索引器。

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. 在请求的**正文**中复制以下 JSON 定义。 请注意字段映射元素；这些映射非常重要，因为它们定义了数据流。 

   `fieldMappings` 在技能集之前处理，它将数据源中的内容发送到索引中的目标字段。 稍后要使用字段映射将未修改的现有内容发送到索引。 如果两端的字段名称和类型相同，则无需映射。

   `outputFieldMappings` 用于技能创建的字段，因此在技能集运行后进行处理。 对 `outputFieldMappings` 中 `sourceFieldNames` 的引用在文档破解或扩充创建它们之前并不存在。 `targetFieldName` 是索引中的字段，在索引架构中定义。

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. 发送请求。 Postman 应返回状态代码 201（确认处理成功）。 

   此步骤预期需要几分钟时间才能完成。 即使数据集较小，分析技能也会消耗大量的计算资源。 

> [!NOTE]
> 创建索引器会调用管道。 如果访问数据、映射输入和输出或操作顺序出现问题，此阶段会显示这些问题。 若要结合代码或脚本更改重新运行管道，可能需要先删除对象。 有关详细信息，请参阅[重置并重新运行](#reset)。

#### <a name="about-indexer-parameters"></a>关于索引器参数

脚本将 ```"maxFailedItems"``` 设置为 -1，指示索引引擎在数据导入期间忽略错误。 这是可接受的，因为演示数据源中的文档很少。 对于更大的数据源，请将值设置为大于 0。

```"dataToExtract":"contentAndMetadata"``` 语句告知索引器从不同的文件格式以及与每个文件相关的元数据中自动提取内容。 

提取内容后，可以设置 ```imageAction```，以从数据源中的图像提取文本。 ```"imageAction":"generateNormalizedImages"``` 配置与 OCR 技能和文本合并技能相结合，告知索引器从图像中提取文本（例如，禁行交通标志中的单词“stop”），并将其嵌入到内容字段中。 此行为将应用到文档中嵌入的图像（例如 PDF 中的图像），以及数据源（例如 JPG 文件）中的图像。

## <a name="4---monitor-indexing"></a>4 - 监视索引

提交“创建索引器”请求后，索引编制和扩充立即开始。 根据定义的认知技能，索引编制可能需要花费一段时间。 若要确定索引器是否仍在运行，请发送以下请求来检查索引器状态。

1. 使用 **GET** 和以下 URL（请将 YOUR-SERVICE-NAME 替换为实际的服务名称）来命名索引器。

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. 检查响应，以了解索引器是否正在运行，或者查看错误和警告信息。  

如果使用的是免费层，则预期会出现以下消息“无法从文档中提取内容或元数据。 提取的文本已截断为 "32768" 个字符”。 出现此消息的原因是，免费层上的 Blob 索引编制存在 [32K 字符提取限制](search-limits-quotas-capacity.md#indexer-limits)。 在更高的层上，此数据集不会出现此消息。 

> [!NOTE]
> 在某些情况下经常出现警告，但不一定意味着存在问题。 例如，如果某个 Blob 容器包含图像文件，而管道不处理图像，则会出现一条警告，指出图像未处理。

## <a name="5---search"></a>5 - 搜索

创建新的字段和信息后，让我们运行一些查询来了解认知搜索对典型搜索方案产生的作用。

回顾前文，我们是从 Blob 内容着手的，整个文档已打包到一个 `content` 字段中。 可以搜索此字段并查找查询的匹配项。

1. 使用 **GET** 和以下 URL（请将 YOUR-SERVICE-NAME 替换为实际的服务名称）来搜索某个字或短语的实例，并返回 `content` 字段和匹配文档的计数。

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   此查询的结果将返回文档内容，这与使用 Blob 索引器但不使用认知搜索管道时获取的结果相同。 此字段是可搜索的，但若要使用分面、筛选器或自动完成，则此字段不起作用。

   ![内容字段输出](media/cognitive-search-tutorial-blob/content-output.png "内容字段输出")
   
1. 第二个查询返回管道创建的一些新字段（人员、组织、位置、languageCode）。 为简洁起见，我们省略了关键短语，但若要查看这些值，应包含关键短语。

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   $select 语句中的字段包含认知服务的自然语言处理功能创建的新信息。 如你所料，结果中出现了一些干扰信息，并且各个文档的返回信息有差异，但在许多情况下，分析模型会生成准确的结果。

   下图显示了 Satya Nadella 在担任 Microsoft 的 CEO 职务后发表的公开信结果。

   ![管道输出](media/cognitive-search-tutorial-blob/pipeline-output.png "管道输出")

1. 若要了解如何利用这些字段，请添加一个分面参数以按位置返回匹配文档的聚合。

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   在此示例中，每个位置有 2 个或 3 个匹配项。

   ![分面输出](media/cognitive-search-tutorial-blob/facet-output.png "分面输出")
   

1. 此最终示例对组织集合应用一个筛选器，以基于 NASDAQ 返回筛选条件的两个匹配项。

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

这些查询演示了对认知搜索创建的新字段使用查询语法和筛选器的多种方式。有关更多查询示例，请参阅[搜索文档 REST API 中的示例](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples)、[简单语法查询示例](search-query-simple-examples.md)和[完整 Lucene 查询示例](search-query-lucene-examples.md)。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重置并重新运行

在管道开发的前期试验阶段，设计迭代的最实用方法是删除 Azure 搜索中的对象，并允许代码重新生成这些对象。 资源名称是唯一的。 删除某个对象后，可以使用相同的名称重新创建它。

使用新定义为文档重新编制索引：

1. 删除索引器、索引和技能集。
2. 修改对象。
3. 在用于运行管道的服务中重新创建。 

可以使用门户删除索引、索引器和技能集，或使用 **DELETE** 并提供每个对象的 URL。 以下命令删除一个索引器。

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

成功删除后会返回状态代码 204。

随着代码的成熟，可能需要优化重新生成策略。 有关详细信息，请参阅[如何重新生成索引](search-howto-reindex.md)。

## <a name="takeaways"></a>要点

本教程演示了通过创建组件部件（数据源、技能集、索引和索引器）生成扩充索引管道的基本步骤。

其中介绍了[预定义的技能集](cognitive-search-predefined-skills.md)、技能集定义，以及通过输入和输出将技能链接在一起的机制。 此外，还提到需要使用索引器定义中的 `outputFieldMappings`，将管道中的扩充值路由到 Azure 搜索服务中的可搜索索引。

最后，介绍了如何测试结果并重置系统以进一步迭代。 本教程提到，针对索引发出查询会返回扩充的索引管道创建的输出。 

## <a name="clean-up-resources"></a>清理资源

完成本教程后，最快的清理方式是删除包含 Azure 搜索服务和 Azure Blob 服务的资源组。 假设已将这两个服务放在同一个组中，则删除该资源组会永久删除其中的所有内容，包括在本教程中创建的服务和任何存储内容。 在门户中，资源组名称显示在每个服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

使用自定义技能自定义或扩展管道。 创建自定义技能并将其添加到技能集可以载入自己编写的文本或图像分析代码。 

> [!div class="nextstepaction"]
> 示例：[创建认知搜索的自定义技能](cognitive-search-create-custom-skill-example.md)