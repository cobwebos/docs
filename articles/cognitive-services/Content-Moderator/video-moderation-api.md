---
title: 在 C# 中分析令人反感的视频内容 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 如何使用适用于 .NET 的内容审查器 SDK 分析视频内容中是否存在各种令人反感的材料
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: 081f512fd421bf46a86f3789eadd75e178e1b6f5
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685312"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>在 C# 中分析令人反感的视频内容

本文中的信息和代码示例可帮助你快速开始使用[适用于 .NET 的内容审查器 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 来扫描视频中的成人或猥亵内容。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>必备条件
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>设置 Azure 资源

内容审查器的视频审查功能可以在 Azure 媒体服务 (AMS) 中免费提供公共预览版的**媒体处理器**。 Azure 媒体服务是用于存储和流式传输视频内容的专业 Azure 服务。 

### <a name="create-an-azure-media-services-account"></a>创建 Azure 媒体服务帐户

遵照[创建 Azure 媒体服务帐户](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account)中的说明订阅 AMS，并创建关联的 Azure 存储帐户。 在该存储帐户中，创建新的 Blob 存储容器。

### <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

在 Azure 门户中导航到新的 AMS 订阅，并从菜单中选择“API 访问”。  选择“通过服务主体连接到 Azure 媒体服务”。  记下“REST API 终结点”字段中的值，因为稍后需要用到。 

在“Azure AD 应用程序”部分，选择“新建”并为新的 Azure AD 应用程序注册命名（例如“VideoModADApp”）。   单击“保存”并等待几分钟时间，让应用程序配置完成。  然后，页面的“Azure AD 应用”部分下应会显示新的应用注册。 

选择该应用注册，并单击其下面的“管理应用程序”按钮。  记下“应用程序 ID”字段中的值，因为稍后需要用到。  选择“设置” > “密钥”，并输入新密钥的说明（例如“VideoModKey”）。 单击“保存”，然后注意新密钥值。  复制此字符串并将其保存到安全的位置。

有关上述过程的全面演练，请参阅 [Azure AD 身份验证入门](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)。

完成上述操作后，可通过两种不同的方式使用视频审查媒体处理器。

## <a name="use-azure-media-services-explorer"></a>使用 Azure 媒体服务资源管理器

Azure 媒体服务资源管理器是 AMS 的用户友好前端。 使用它可以浏览 AMS 帐户、上传视频，以及通过内容审查器媒体处理器扫描内容。 从 [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases) 下载并安装此工具，或参阅 [Azure 媒体服务资源管理器博客文章](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/)了解详细信息。

![带有内容审查器的 Azure 媒体服务资源管理器](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中创建新的**控制台应用 (.NET Framework)** 项目并将其命名为 **VideoModeration**。 
1. 如果解决方案中有其他项目，请将此项目选为单一启动项目。
1. 获取所需的 NuGet 包。 右键单击解决方案资源管理器中的项目，选择“管理 NuGet 包”，然后找到并安装以下包： 
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>添加视频审查代码

接下来，将代码从本指南复制并粘贴到项目中，以便实施基本的内容审查方案。

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

将以下 `using` 语句添加到 _Program.cs_ 文件顶部。

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>设置资源引用

向 Program.cs 中的 Program 类添加以下静态字段   。 这些字段包含连接到 AMS 订阅所需的信息。 请在这些字段内填充在上述步骤中获取的值。 请注意，`CLIENT_ID` 是 Azure AD 应用的“应用程序 ID”值，`CLIENT_SECRET` 是为该应用创建的“VideoModKey”的值。

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

若要使用本地视频文件（最简单的情况），请将其添加到项目，并以 `INPUT_FILE` 值的形式输入其路径（相对路径相对于执行目录）。

还需要在当前目录中创建 _preset.json_ 文件，并使用它来指定版本号。 例如：

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>加载输入视频

**Program** 类的 **Main** 方法依次创建 Azure 媒体上下文和 Azure 存储上下文（如果视频位于 Blob 存储中）。 剩余代码会扫描本地文件夹、Azure 存储容器内的一个或多个 blob 中的视频。 你可以通过注释掉其他代码行来尝试所有选项。

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>创建 Azure 媒体上下文

将以下方法添加到 **Program** 类。 这会使用你的 AMS 凭据来实现与 AMS 通信。

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>添加代码以创建 Azure 存储上下文

将以下方法添加到 **Program** 类。 使用从存储凭据创建的存储上下文来访问 Blob 存储。

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>添加代码以从本地文件和 blob 创建 Azure 媒体资产

内容审查器媒体处理器对 Azure 媒体服务平台内的  “资产”上运行作业。
这些方法会从本地文件或关联的 blob 创建资产。

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>添加代码以扫描容器内的视频集合（作为 blob）

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>添加方法以运行内容审查器作业

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>添加帮助程序函数

这些方法会从 Azure 媒体服务资产中下载内容审查器输出文件 (JSON) 并帮助跟踪审查作业的状态，以便该程序可以将运行状态记录到控制台。

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>运行程序并查看输出

内容审查作业完成后，分析 JSON 响应。 它由以下元素组成：

- 视频信息摘要
-  截图作为“片段” 
-  基于“成人”和“不雅”分数，关键帧作为具有 reviewRecommended“（= true 或 false）”标志的“事件”    
-     “开始时间”、“持续时间”、“总持续时间”和“时间戳”以“刻度”为单位。 除以  “时间刻度”以获得数字（以秒为单位）。
 
> [!NOTE]
> - `adultScore` 表示可能存在某些情况下可能被视为色情或成人性质的内容以及预测分数。
> - `racyScore` 表示可能存在某些情况下可能被视为性暗示或过于成熟的内容以及预测分数。
> - `adultScore` 和 `racyScore` 介于 0 和 1 之间。 评分越高，模型预测类别可能适用的可能性越高。 此预览版依赖于统计模型，而不是人工编码结果。 我们建议你对自己的内容进行测试，以确定每个类别是否符合要求。
> - `reviewRecommended` 为 true 或 false，具体情况取决于内部评分阈值。 客户应评估是使用该值，还是根据他们的内容策略确定自定义阈值。

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>后续步骤

了解如何从你的审查输出生成[视频评论](video-reviews-quickstart-dotnet.md)。

将[脚本审查](video-transcript-moderation-review-tutorial-dotnet.md)添加到你的视频评论中。

了解有关如何生成[完整视频和脚本审查解决方案](video-transcript-moderation-review-tutorial-dotnet.md)的详细教程。

为适用于 .NET 的此内容审查器快速入门以及其他内容审查器快速入门[下载 Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。
