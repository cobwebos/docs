---
title: Azure 内容审查器 - 视频审查 | Microsoft Docs
description: 使用视频审查扫描可能的成人和不雅内容。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366633"
---
# <a name="video-moderation"></a>视频审查

现在，联机查看器可跨热门和区域的社交媒体网站生成数十亿个视频视图，并且在不断增加。 通过应用基于机器学习的服务来预测可能的成人和不雅内容，可降低审查工作的成本。

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>注册内容审查器媒体处理器（公共预览版）

### <a name="create-a-free-azure-account"></a>创建免费 Azure 帐户

如果没有 Azure 帐户，[从此处开始](https://azure.microsoft.com/free/)可以创建免费 Azure 帐户。

### <a name="create-an-azure-media-services-account"></a>创建 Azure 媒体服务帐户

内容审查器的视频功能可以在 Azure 媒体服务 (AMS) 中免费提供公共预览版的媒体处理器。

在 Azure 订阅中[创建 Azure 媒体服务帐户](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account)。

### <a name="get-azure-active-directory-credentials"></a>获取 Azure Active Directory 凭据

   1. 阅读 [Azure 媒体服务门户文章](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)以了解如何使用 Azure 门户获取 Azure AD 身份验证凭据。
   1. 阅读 [Azure 媒体服务 .NET 文章](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad)以了解如何将 Azure Active Directory 凭据与 .NET SDK 配合使用。

   > [!NOTE]
   > 本快速入门中的示例代码使用这两篇文章中所述的“服务主体身份验证”方法。

获取 AMS 凭据后，有两种方法可以尝试内容审查器媒体处理器。

## <a name="use-azure-media-services-explorer"></a>使用 Azure 媒体服务资源管理器

使用交互式 [Azure 媒体服务 (AMS) 资源管理器](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/)浏览你的 AMS 帐户、上载视频并使用内容审查器媒体处理器进行扫描。 从 GitHub [下载并安装它](https://github.com/Azure/Azure-Media-Services-Explorer/releases)，并使用 AMS SDK [浏览源代码](http://github.com/Azure/Azure-Media-Services-Explorer)进行深入了解。

![带有内容审查器的 Azure 媒体服务资源管理器](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>使用 Visual Studio 和 C# 编写的 .NET 快速入门

1. 向解决方案添加新的控制台应用 (.NET Framework) 项目。

   在示例代码中，将项目命名为“VideoModeration”。

1. 选择此项目作为解决方案的单个启动项目。

### <a name="install-required-packages"></a>安装所需程序包

安装 [NuGet](https://www.nuget.org/) 上提供的以下 NuGet 包。

- windowsazure.mediaservices
- windowsazure.mediaservices.extensions

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

修改程序的 using 语句。

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


### <a name="initialize-application-specific-settings"></a>初始化特定于应用程序的设置

向 Program.cs 中的 Program 类添加以下静态字段。

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>创建预设文件 (json)

在当前目录中创建带有版本号的 JSON 文件。

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>将以下代码添加到 main 方法

main 方法会依次创建 Azure 媒体上下文和 Azure 存储上下文，以防视频位于 blob 存储中。
剩余代码会扫描本地文件夹、Azure 存储容器内的一个或多个 blob 中的视频。
你可以通过注释掉其他代码行来尝试所有选项。

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>添加代码以创建 Azure 媒体上下文

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>添加代码以创建 Azure 存储上下文
使用从存储凭据创建的存储上下文来访问 blob 存储。

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>添加代码以从本地文件和 blob 创建 Azure 媒体资产
内容审查器媒体处理器对 Azure 媒体服务平台内的“资产”上运行作业。
这些方法会从本地文件或关联的 blob 创建资产。

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>添加代码以扫描容器内的视频集合（作为 blob）

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
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

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>添加方法以运行内容审查器作业

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
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

### <a name="add-a-couple-of-helper-functions"></a>添加几个帮助程序函数

这些方法会从 Azure 媒体服务资产中下载内容审查器输出文件 (JSON) 并帮助跟踪审查作业的状态，以便该程序可以将运行状态记录到控制台。

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

### <a name="run-the-program-and-review-the-output"></a>运行程序并检查输出

内容审查作业完成后，分析 JSON 响应。 它由以下元素组成：

- 视频信息摘要
- 截图作为“片段”
- 基于“成人”和“不雅”分数，关键帧作为具有 reviewRecommended“（= true 或 false）”标志的“事件”
- “开始时间”、“持续时间”、“总持续时间”和“时间戳”以“刻度”为单位。 除以“时间刻度”以获得数字（以秒为单位）。
 
> [!NOTE]

> - `adultScore` 表示可能存在某些情况下可能被视为色情或成人性质的内容以及预测分数。
> - `racyScore` 表示可能存在某些情况下可能被视为性暗示或过于成熟的内容以及预测分数。
> - `adultScore` 和 `racyScore` 介于 0 和 1 之间。 分数越高，模型预测类别可能适用的可能性越高。 此预览版依赖于统计模型，而不是人工编码结果。 我们建议对你自己的内容进行测试，以确定每个类别是否满足你的需求。
> - `reviewRecommended` 为 true 或 false，具体情况取决于内部分数阈值。 客户应评估是使用该值，还是根据他们的内容策略确定自定义阈值。
>

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

## <a name="next-steps"></a>后续步骤

了解如何从你的审查输出生成[视频评论](video-reviews-quickstart-dotnet.md)。

将[脚本审查](video-transcript-moderation-review-tutorial-dotnet.md)添加到你的视频评论中。

了解有关如何生成[完整视频和脚本审查解决方案](video-transcript-moderation-review-tutorial-dotnet.md)的详细教程。

为适用于 .NET 的此内容审查器快速入门以及其他内容审查器快速入门[下载 Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。
