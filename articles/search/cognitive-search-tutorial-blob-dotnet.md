---
title: 教程：基于 Azure Blob 的 C# 和 AI
titleSuffix: Azure Cognitive Search
description: 通过一个示例来逐步了解如何使用 C# 和 Azure 认知搜索 .NET SDK 基于 Blob 存储中的内容进行文本提取和自然语言处理。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 169a33d12e98235dcb4e4f317dbb8d91eb7446a4
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851131"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>教程：使用 C# 和 AI 从 Azure Blob 生成可搜索的内容

如果在 Azure Blob 存储中有使用非结构化文本或图像，则 [AI 扩充管道](cognitive-search-concept-intro.md)可以提取信息，并创建可用于全文搜索或知识挖掘方案的新内容。 本 C# 教程对图像应用光学字符识别 (OCR)，并执行自然语言处理来创建可在查询、分面和筛选器中利用的新字段。

本教程使用 C# 和 [.NET SDK](https://aka.ms/search-sdk) 执行以下任务：

> [!div class="checklist"]
> * 从 Azure Blob 存储中的应用程序文件和图像开始。
> * 定义一个管道用于添加 OCR、提取文本、检测语言以及识别实体和关键短语。
> * 定义用于存储输出（原始内容，加上管道生成的名称/值对）的索引。
> * 执行管道以开始转换和分析，以及创建和加载索引。
> * 使用全文搜索和丰富的查询语法浏览结果。

如果你没有 Azure 订阅，请在开始之前建立一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

+ [Azure 存储](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [创建](search-create-service-portal.md)或[查找现有搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 可在本教程中使用免费服务。 免费搜索服务限制为三个索引、三个索引器和三个数据源。 本教程每样创建一个。 在开始之前，请确保服务中有足够的空间可接受新资源。

## <a name="download-files"></a>下载文件

1. 打开此 [OneDrive 文件夹](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)，然后单击左上角的“下载”将文件复制到计算机。  

1. 右键单击 zip 文件并选择“全部提取”。  有 14 个不同类型的文件。 本教程将使用所有这些文件。

## <a name="1---create-services"></a>1 - 创建服务

本教程使用 Azure 认知搜索编制索引和进行查询、使用后端的认知服务进行 AI 扩充，并使用 Azure Blob 存储提供数据。 本教程使用的认知服务不超过每日为每个索引器免费分配 20 个事务这一限制，因此，只需要创建搜索和存储服务。

如果可能，请在同一区域和资源组中创建这两个服务，使它们相互靠近并易于管理。 在实践中，Azure 存储帐户可位于任意区域。

### <a name="start-with-azure-storage"></a>从 Azure 存储开始

1. [登录到 Azure 门户](https://portal.azure.com/)并单击“+ 创建资源”。 

1. 搜索“存储帐户”，并选择“Microsoft 的存储帐户”产品/服务。 

   ![创建存储帐户](media/cognitive-search-tutorial-blob/storage-account.png "创建存储帐户")

1. 在“基本信息”选项卡中，必须填写以下项。 对于其他任何字段，请接受默认设置。

   + 资源组  。 选择现有的资源组或创建新资源组，但对于所有服务请使用相同的组，以便可以统一管理这些服务。

   + **存储帐户名称**。 如果你认为将来可能会用到相同类型的多个资源，请使用名称来区分类型和区域，例如 *blobstoragewestus*。 

   + **位置**。 如果可能，请选择 Azure 认知搜索和认知服务所用的相同位置。 使用一个位置可以避免带宽费用。

   + **帐户类型**。 选择默认设置“StorageV2 (常规用途 v2)”  。

1. 单击“查看 + 创建”以创建服务。 

1. 创建后，单击“转到资源”打开“概述”页。 

1. 单击“Blob”服务。 

1. 单击“+ 容器”创建容器，并将其命名为 *basic-demo-data-pr*。 

1. 选择“basic-demo-data-pr”，然后单击“上传”打开下载文件所保存到的文件夹。   选择所有 14 个文件，然后单击“确定”以上传。 

   ![上传示例文件](media/cognitive-search-quickstart-blob/sample-data.png "上传示例文件")

1. 在退出 Azure 存储之前获取一个连接字符串，以便可以在 Azure 认知搜索中构建连接。 

   1. 向后浏览到存储帐户的“概述”页（我们使用了 *blobstragewestus* 作为示例）。 
   
   1. 在左侧导航窗格中，选择“访问密钥”并复制其中一个连接字符串。  

   连接字符串是类似于以下示例的 URL：

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. 将连接字符串保存到记事本中。 稍后在设置数据源连接时需要用到它。

### <a name="cognitive-services"></a>认知服务

AI 扩充由认知服务（包括用于自然语言和图像处理的文本分析与计算机视觉）提供支持。 如果你的目标是完成实际原型或项目，则此时应预配认知服务（在 Azure 认知搜索所在的同一区域中），以便可将认知服务附加到索引操作。

但是，对于本练习，可以跳过资源预配，因为 Azure 认知搜索在幕后可以连接到认知服务，并为每个索引器运行提供 20 个免费事务。 由于本教程使用 7 个事务，因此免费的分配已足够。 对于大型项目，请计划在即用即付 S0 层预配认知服务。 有关详细信息，请参阅[附加认知服务](cognitive-search-attach-cognitive-services.md)。

### <a name="azure-cognitive-search"></a>Azure 认知搜索

第三个组件是可以[在门户中创建](search-create-service-portal.md)的 Azure 认知搜索。 可使用免费层完成本演练。 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>获取 Azure 认知搜索的管理 API 密钥和 URL

必须有 Azure 认知搜索服务 URL 和访问密钥，才能与此服务交互。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

   此外，获取查询密钥。 最好使用只读权限发出查询请求。

   ![获取服务名称以及管理密钥和查询密钥](media/search-get-started-nodejs/service-name-and-keys.png)

具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="2---set-up-your-environment"></a>2 - 设置环境

首先，打开 Visual Studio，并新建能在 .NET Core 上运行的控制台应用项目。

### <a name="install-nuget-packages"></a>安装 NuGet 包

[Azure 认知搜索 .NET SDK](https://aka.ms/search-sdk) 由一些客户端库组成。借助这些库，不仅可以管理索引、数据源、索引器和技能集，还能上传和管理文档并执行查询，所有这些操作都无需处理 HTTP 和 JSON 的详细信息。 这些客户端库全部作为 NuGet 包进行分发。

对于此项目，请安装版本 9 或更高版本的 `Microsoft.Azure.Search` NuGet 包。

1. 打开包管理器控制台。 选择“工具”   > “NuGet 包管理器”   > “包管理器控制台”  。 

1. 导航到 [Microsoft.Azure.Search NuGet 包页](https://www.nuget.org/packages/Microsoft.Azure.Search)。

1. 选择最新版本（9 或以上）。

1. 复制包管理器命令。

1. 返回到包管理器控制台，运行在上一步骤中复制的命令。

接下来，安装最新的 `Microsoft.Extensions.Configuration.Json` NuGet 包。

1. 选择“工具” > “NuGet 包管理器” > “管理解决方案...的 NuGet 包”。    

1. 单击“浏览”并搜索 `Microsoft.Extensions.Configuration.Json` NuGet 包。  

1. 选择该包和你的项目，确认版本是否为最新稳定版，然后单击“安装”。 

### <a name="add-service-connection-information"></a>添加服务连接信息

1. 在解决方案资源管理器中右键单击该项目，并选择“添加” > “新建项...”。   

1. 将文件命名为“`appsettings.json`”，并选择“添加”  。 

1. 将此文件包含在输出目录中。
    1. 右键单击 `appsettings.json` 并选择“属性”。  
    1. 将“复制到输出目录”的值更改为“如果较新则复制”。  

1. 将以下 JSON 复制到新 JSON 文件中。

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

添加搜索服务信息和 Blob 存储帐户信息。 请注意，可以从上一部分所述的服务预配步骤获取此信息。

### <a name="add-namespaces"></a>添加命名空间

在 `Program.cs` 中添加以下命名空间。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>创建客户端

在 `Main` 下创建 `SearchServiceClient` 类的实例。

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` 使用应用程序的配置文件 (appsettings.json) 中存储的值创建新的 `SearchServiceClient`。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> `SearchServiceClient` 类管理与搜索服务的连接。 为了避免打开太多连接，应尝试在应用程序中共享 `SearchServiceClient` 的单个实例（如果可能）。 它的方法在启用此类共享时是线程安全的。
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>添加在程序失败时退出程序的函数

本教程旨在帮助你了解索引管道的每个步骤。 如果存在阻止程序创建数据源、技能组、索引或索引器的严重问题，程序将输出错误消息并退出，以便你了解并解决该问题。

将 `ExitProgram` 添加到 `Main`，以处理需要程序退出的情况。

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - 创建管道

在 Azure 认知搜索中，AI 处理是在索引编制（或数据引入）期间发生的。 本演练部分将创建四个对象：数据源、索引定义、技能集和索引器。 

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

`SearchServiceClient` 具有 `DataSources` 属性。 此属性提供创建、列出、更新或删除 Azure 认知搜索数据源所需的全部方法。

通过调用 `serviceClient.DataSources.CreateOrUpdate(dataSource)`，新建 `DataSource` 实例。 `DataSource.AzureBlobStorage` 要求必须指定数据源名称、连接字符串和 Blob 容器名称。

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

为了让请求成功，此方法将返回已创建的数据源。 如果请求有问题（如参数无效），此方法将抛出异常。

现在，在 `Main` 中添加一行，以调用刚刚添加的 `CreateOrUpdateDataSource` 函数。

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

生成并运行解决方案。 由于这是发出的第一个请求，请检查 Azure 门户，确认是否在 Azure 认知搜索中创建了数据源。 在搜索服务的仪表板页上，检查“数据源”磁贴中是否包含一个新项。 可能需要等待几分钟让门户页刷新。

  ![门户中的“数据源”磁贴](./media/cognitive-search-tutorial-blob/data-source-tile.png "门户中的“数据源”磁贴")

### <a name="step-2-create-a-skillset"></a>步骤 2：创建技能集

在此部分中，你将定义一组要应用于数据的扩充步骤。 每个扩充步骤称为“技能”  ，一组扩充步骤称为“技能集”  。 本教程对技能集使用以下[内置认知技能](cognitive-search-predefined-skills.md)：

+ [光学字符识别](cognitive-search-skill-ocr.md)：用于识别图像文件中的印刷文本和手写文本。

+ [文本合并](cognitive-search-skill-textmerger.md)：用于将字段集合中的文本合并到单个字段中。

+ [语言检测](cognitive-search-skill-language-detection.md)：识别内容的语言。

+ [文本拆分](cognitive-search-skill-textsplit.md)：用于先将大段内容拆分为较小区块，再调用关键短语提取技能和实体识别技能。 关键短语提取和实体识别接受不超过 50,000 个字符的输入。 有几个示例文件需要拆分才能保留在此限制范围内。

+ [实体识别](cognitive-search-skill-entity-recognition.md)：从 Blob 容器中的内容提取组织名称。

+ [关键短语提取](cognitive-search-skill-keyphrases.md)：取出最关键的短语。

在初始处理期间，Azure 认知搜索会破译每个文档，以读取不同文件格式的内容。 从源文件中找到的文本将放入一个生成的 ```content``` 字段（每个文档对应一个字段）。 因此，请将输入设置为 ```"/document/content"```，以使用此文本。 

输出可以映射到索引、用作下游技能的输入，或者既映射到索引又用作输入（在语言代码中就是这样）。 在索引中，语言代码可用于筛选。 文本分析技能使用语言代码作为输入来告知有关断字的语言规则。

若要详细了解技能集的基础知识，请参阅[如何定义技能集](cognitive-search-defining-skillset.md)。

### <a name="ocr-skill"></a>OCR 技术

OCR  技能从图像中提取文本。 此技能假定存在“normalized_images”字段。 为了生成此字段，本教程稍后会将索引器定义中的 ```"imageAction"``` 配置设置为 ```"generateNormalizedImages"```。

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>合并技能

在此部分中，你将创建合并  技能，用于将文档内容字段与 OCR 技能生成的文本合并。

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>语言检测技能

语言检测  技能检测输入文本的语言，并报告在请求中提交的每个文档的单一语言代码。 我们会将语言检测  技能的输出用作文本拆分  技能的输入的一部分。

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>文本拆分技能

下面的拆分  技能按页面拆分文本，并将页面长度限制为 `String.Length` 度量的 4,000 个字符。 此算法会尝试将文本拆分为最大为 `maximumPageLength` 的区块。 在下面的示例中，此算法会尽可能在句子边界断开句子，所以区块大小可能略小于 `maximumPageLength`。

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>实体识别技能

设置此 `EntityRecognitionSkill` 实例是为了识别类别类型 `organization`。 此外，实体识别  技能还可以识别类别类型 `person` 和 `location`。

请注意，“context”字段设置为包含星号的 ```"/document/pages/*"```；也就是说，将对 ```"/document/pages"``` 下的每个页面都调用扩充步骤。

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>关键短语提取技能

与刚刚创建的 `EntityRecognitionSkill` 实例一样，关键短语提取  技能对文档的各个页面都调用。

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>生成并创建技能集

使用已创建的技能来生成 `Skillset`。

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

将以下行添加到 `Main`。

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>步骤 3：创建索引

本部分通过指定要在可搜索索引中包含的字段以及每个字段的搜索特性，来定义索引架构。 字段具有某种类型，并可以采用特性来确定字段的使用方式（可搜索、可排序，等等）。 索引中的字段名称不一定要与源中的字段名称完全匹配。 在稍后的步骤中，我们将在索引器中添加字段映射以连接源-目标字段。 针对此步骤，请使用搜索应用程序相关的字段命名约定来定义索引。

本演练使用以下字段和字段类型：

| field-names: | `id`       | content   | languageCode | keyPhrases         | 组织     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>创建 DemoIndex 类

此索引的字段是使用模型类进行定义。 模型类的每个属性都具有一些特性，这些特性决定了相应索引字段的与搜索相关的行为。 

接下来，将把模型类添加到新 C# 文件中。 右键单击项目，并依次选择“添加”   > “新项...”  。选择“类”，并将文件命名为“`DemoIndex.cs`”，再选择“添加”  。

请务必指明要使用 `Microsoft.Azure.Search` 和 `Microsoft.Azure.Search.Models` 命名空间中的类型。

将下面的模型类定义添加到 `DemoIndex.cs` 中，并将它添加到要在其中创建索引的同一命名空间中。

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
``` -->

至此，已定义模型类。返回到 `Program.cs`，可以轻松创建索引定义了。 此索引的名称为 `demoindex`。 如果已存在同名的索引，则会删除该索引。

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

在测试期间，你可能会发现要多次尝试创建索引。 因此，请先检查要创建的索引是否已存在，再尝试创建索引。

将以下行添加到 `Main`。

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```
 -->

若要详细了解如何定义索引，请参阅[创建索引（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)。

### <a name="step-4-create-and-run-an-indexer"></a>步骤 4：创建并运行索引器

到目前为止，我们已创建数据源、技能集和索引。 这三个组件属于某个[索引器](search-indexer-overview.md)，该索引器将每个片段一同提取到单个多阶段操作。 若要在索引器中将这些组件捆绑在一起，必须定义字段映射。

+ 先处理 fieldMapping，再处理技能集；将数据源中的源字段映射到索引中的目标字段。 如果两端的字段名称和类型相同，则无需映射。

+ 先处理技能集，再处理 outputFieldMapping；引用不存在的 sourceFieldName，直到文档破解或扩充功能创建了它们。 targetFieldName 是索引中的字段。

除了将输入挂钩到输出外，还可以使用字段映射来平展数据结构。 有关详细信息，请参阅[如何将扩充字段映射到可搜索索引](cognitive-search-output-field-mapping.md)。

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
将以下行添加到 `Main`。

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

创建索引器预计需要一段时间才能完成。 即使数据集较小，分析技能也会消耗大量的计算资源。 某些技能（例如图像分析）会长时间运行。

> [!TIP]
> 创建索引器会调用管道。 如果访问数据、映射输入和输出或操作顺序出现问题，此阶段会显示这些问题。

### <a name="explore-creating-the-indexer"></a>探索如何创建索引器

代码将 ```"maxFailedItems"``` 设置为 -1，指示索引引擎在数据导入期间忽略错误。 此设置非常有用，因为演示数据源中的文档很少。 对于更大的数据源，请将值设置为大于 0。

另请注意，```"dataToExtract"``` 设置为 ```"contentAndMetadata"```。 该语句告知索引器从不同的文件格式以及与每个文件相关的元数据中自动提取内容。

提取内容后，可以设置 `imageAction`，以从数据源中的图像提取文本。 将设置为 ```"generateNormalizedImages"``` 配置的 ```"imageAction"``` 与 OCR 技能和文本合并技能相结合，指示索引器从图像中提取文本（例如，交通停车标志中的“停”一词），并将它嵌入为内容字段的一部分。 此行为将应用到文档中嵌入的图像（例如 PDF 中的图像），以及数据源（例如 JPG 文件）中的图像。

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - 监视索引

定义索引器后，提交请求时会自动运行索引器。 根据定义的认知技能，索引编制花费的时间可能会超出预期。 若要确定索引器是否仍在运行，请使用 `GetStatus` 方法。

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` 表示索引器的当前状态和执行历史记录。

处理某些源文件和技能的组合时经常会出现警告，这并不总是意味着出现了问题。 在本教程中，警告是良性的（例如，JPEG 文件中没有文本输入）。

将以下行添加到 `Main`。

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - 搜索

索引编制完成后，可以运行查询来返回各个字段的内容。 默认情况下，Azure 认知搜索返回前 50 条结果。 由于样本数据较小，因此使用默认设置即可正常操作。 但是，在处理较大的数据集时，可能需要在查询字符串中包含参数来返回更多结果。 有关说明，请参阅[如何将 Azure 认知搜索中的结果分页](search-pagination-page-layout.md)。

作为验证步骤，请查询所有字段的索引。

将以下行添加到 `Main`。

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` 使用应用程序的配置文件 (appsettings.json) 中存储的值创建新的 `SearchIndexClient`。 请注意，使用的是搜索服务查询 API 密钥而不是管理员密钥。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

将以下代码添加到 `Main`。 第一个 try-catch 返回索引定义，其中包含每个字段的名称、类型和属性。 第二个 try-catch 是参数化查询，其中的 `Select` 指定要包含在结果中的字段，例如 `organizations`。 `"*"` 搜索字符串返回单个字段的所有内容。

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

针对本练习中的其他字段（content、languageCode、keyPhrases 和 organizations）重复上述步骤。 可以使用逗号分隔列表通过 [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) 属性返回多个字段。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重置并重新运行

在开发的前期试验阶段，设计迭代的最实用方法是，删除 Azure 认知搜索中的对象，并允许代码重新生成它们。 资源名称是唯一的。 删除某个对象后，可以使用相同的名称重新创建它。

本教程的示例代码将检查现有对象并将其删除，使你能够重新运行代码。

也可以使用门户来删除索引、索引器、数据源和技能集。

## <a name="takeaways"></a>要点

本教程演示了通过创建组件部件（数据源、技能集、索引和索引器）生成扩充索引管道的基本步骤。

其中介绍了[内置技能组](cognitive-search-predefined-skills.md)、技能集定义，以及通过输入和输出将技能链接在一起的机制。 此外，还提到需要使用索引器定义中的 `outputFieldMappings`，将管道中的扩充值路由到 Azure 认知搜索服务中的可搜索索引。

最后，介绍了如何测试结果并重置系统以进一步迭代。 本教程提到，针对索引发出查询会返回扩充的索引管道创建的输出。 此外，本教程还介绍了如何检查索引器状态，以及在重新运行管道之前要删除的对象。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时删除不再需要的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接在门户中查找和管理资源。

## <a name="next-steps"></a>后续步骤

熟悉 AI 扩充管道中的所有对象后，接下来让我们更详细地了解技能集定义和各项技能。

> [!div class="nextstepaction"]
> [如何创建技能集](cognitive-search-defining-skillset.md)
