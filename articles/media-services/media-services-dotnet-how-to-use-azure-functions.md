---
title: "开发使用媒体服务的 Azure Functions"
description: "本主题说明如何在 Azure 门户中开始开发使用媒体服务的 Azure Functions。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ca7c89dc5f56f1bf4c300874111e65048e88abcf
ms.lasthandoff: 04/12/2017


---
#<a name="develop-azure-functions-with-media-services"></a>开发使用媒体服务的 Azure Functions

本主题介绍如何在 Azure 门户中开始开发使用媒体服务的 Azure Functions。 

还可按下“部署到 Azure”按钮进行现有[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) 的部署。 此存储库包含 Azure Functions 示例。这些示例使用 Azure 媒体服务来演示有关直接从 Blob 存储引入内容、编码以及将内容写回 Blob 存储的工作流。 此存储库还包含演示如何通过 WebHook 和 Azure 队列监视作业通知的示例。 也可根据[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) 存储库中的示例进行 Functions 开发。 

本主题说明如何开始创建使用媒体服务的 Azure Functions。 本主题中定义的 Azure Function 可监视新 MP4 文件中名为**输入**的存储帐户容器。 将文件放入存储容器后，blob 触发器就会执行此函数。

## <a name="prerequisites"></a>先决条件

- 必须先具有有效的 Azure 帐户，然后才能创建你的第一个函数。 如果还没有 Azure 帐户， [可以使用免费帐户](https://azure.microsoft.com/free/)。
- 若要创建针对 Azure 媒体服务 (AMS) 帐户执行操作或者侦听媒体服务发送的事件的 Azure Functions，应该根据[此文](media-services-portal-create-account.md)中所述创建一个 AMS 帐户。
- 了解[如何使用 Azure Functions](../azure-functions/functions-overview.md)。 另请参阅：
    - [Azure Functions HTTP 和 webhook 绑定](../azure-functions/functions-triggers-bindings.md)
    - [如何配置 Azure Function App 设置](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
    
## <a name="considerations"></a>注意事项

-  消耗量计划下运行的 Azure Functions 具有 5 分钟超时限制。

## <a name="create-a-function-app"></a>创建 Function App

1. 转到 [Azure 门户](http://portal.azure.com)，然后使用你的 Azure 帐户登录。
2. 根据[此文](../azure-functions/functions-create-function-app-portal.md)中所述创建 Function App。

>[!NOTE]
> **StorageConnection** 环境变量中指定的存储帐户需与应用位于同一区域（详见下一步）。

## <a name="configure-function-app-settings"></a>配置 Function App 设置

开发媒体服务函数时，可随时添加要在整个函数中使用的环境变量。 若要配置应用设置，请单击“配置应用设置”链接。 有关详细信息，请参阅[如何配置 Azure Function App 设置](../azure-functions/functions-how-to-use-azure-function-app-settings.md)。 

例如：

![设置](./media/media-services-azure-functions/media-services-azure-functions001.png)

本文中定义的函数假定应用设置中具备以下环境变量：

**AMSAccount**：AMS 帐户名称（例如 testams）

**AMSKey**：AMS 帐户密钥（例如 IHOySnH+XX3LGPfraE5fKPl0EnzvEPKkOPKCr59aiMM=）

**MediaServicesStorageAccountName**：存储帐户名称（例如 testamsstorage）

**MediaServicesStorageAccountKey**：存储帐户密钥（例如 xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXXX==）

**StorageConnection**：存储连接（例如 DefaultEndpointsProtocol=https;AccountName=testamsstorage;AccountKey=xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXX==）

## <a name="create-a-function"></a>创建函数

部署 Function App 后，可在**应用服务** Azure Functions 中找到它。

1. 选择你的 Function App，然后单击“新建函数”。
2. 选择“C#”语言和“数据处理”方案。
3. 选择“BlobTrigger”模板。 只要将 blob 上传到**输入**容器，就会触发此函数。 下一步的“路径”中指定了**输入**名称。

    ![文件](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. 选择“BlobTrigger”后，页面上将显示更多控件。

    ![文件](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. 单击“创建” 。 


## <a name="files"></a>文件

Azure 函数与代码文件以及本部分所述的其他文件相关联。 默认情况下，函数与 **function.json** 和 **run.csx** (C#) 文件相关联。 需要添加 **project.json** 文件。 本部分的余下内容介绍这些文件的定义。

![文件](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Function.json 文件定义函数绑定和其他配置设置。 运行时使用此文件确定要监视的事件，以及如何将数据传入函数执行和从函数执行返回数据。 有关详细信息，请参阅 [Azure Functions HTTP 和 webhook 绑定](../azure-functions/functions-reference.md#function-code)。

>[!NOTE]
>将 **disabled** 属性设置为“true”，阻止函数执行。 


下面是 **function.json** 文件的示例。

    {
    "bindings": [
      {
        "name": "myBlob",
        "type": "blobTrigger",
        "direction": "in",
        "path": "input/{fileName}.mp4",
        "connection": "StorageConnection"
      }
    ],
    "disabled": false
    }

### <a name="projectjson"></a>project.json

project.json 文件包含依赖项。 下面是一个 **project.json** 文件示例，其中包含 Nuget 所需的 .NET Azure 媒体服务包。 请注意，版本号将随包的最新更新而改变，因此应确认最新版本。 

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

这是函数的 C# 代码。  下方定义的函数可监视新 MP4 文件中名为**输入**的存储帐户容器（即路径中指定的容器）。 将文件放入存储容器后，blob 触发器就会执行此函数。
    
本部分定义的示例演示了 

1. 如何将资产引入媒体服务帐户（通过将 blob 复制到 AMS 资产），以及 
2. 如何提交使用 Media Encoder Standard“自适应流式处理”预设的编码作业。

在实际方案中，很可能需要跟踪作业进度，然后发布编码的资产。 有关详细信息，请参阅[使用 Azure WebHook 监视媒体服务作业通知](media-services-dotnet-check-job-progress-with-webhooks.md)。 有关更多示例，请参阅[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)。  

定义后，请单击“保存并运行”。

    #r "Microsoft.WindowsAzure.Storage"
    #r "Newtonsoft.Json"
    #r "System.Web"

    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Auth;

    private static readonly string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    private static readonly string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");

    static string _storageAccountName = Environment.GetEnvironmentVariable("MediaServicesStorageAccountName");
    static string _storageAccountKey = Environment.GetEnvironmentVariable("MediaServicesStorageAccountKey");

    private static CloudStorageAccount _destinationStorageAccount = null;

    // Field for service context.
    private static CloudMediaContext _context = null;
    private static MediaServicesCredentials _cachedCredentials = null;

    public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
    {
        // NOTE that the variables {fileName} here come from the path setting in function.json
        // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
        // was dropped into the input container for the function. 

        // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
        // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

        log.Info($"C# Blob trigger function processed: {fileName}.mp4");
        log.Info($"Using Azure Media Services account : {_mediaServicesAccountName}");


        try
        {
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                _mediaServicesAccountName,
                _mediaServicesAccountKey);

        // Used the chached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        // Step 1:  Copy the Blob into a new Input Asset for the Job
        // ***NOTE: Ideally we would have a method to ingest a Blob directly here somehow. 
        // using code from this sample - https://azure.microsoft.com/en-us/documentation/articles/media-services-copying-existing-blob/

        StorageCredentials mediaServicesStorageCredentials =
            new StorageCredentials(_storageAccountName, _storageAccountKey);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

        }
        catch (Exception ex)
        {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


    public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
        IAsset newAsset = null;

        try{
            Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
            newAsset = await copyAssetTask;
            log.Info($"Asset Copied : {newAsset.Id}");
        }
        catch(Exception ex){
            log.Info("Copy Failed");
            log.Info($"ERROR : {ex.Message}");
            throw ex;
        }

        return newAsset;
    }

    /// <summary>
    /// Creates a new asset and copies blobs from the specifed storage account.
    /// </summary>
    /// <param name="blob">The specified blob.</param>
    /// <returns>The new asset.</returns>
    public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
    {
         //Get a reference to the storage account that is associated with the Media Services account. 
        StorageCredentials mediaServicesStorageCredentials =
        new StorageCredentials(_storageAccountName, _storageAccountKey);
        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);

        // Create a new asset. 
        var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
        log.Info($"Created new asset {asset.Name}");

        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
        TimeSpan.FromHours(4), AccessPermissions.Write);
        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

        // Get the destination asset container reference
        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

        try{
        assetContainer.CreateIfNotExists();
        }
        catch (Exception ex)
        {
        log.Error ("ERROR:" + ex.Message);
        }

        log.Info("Created asset.");

        // Get hold of the destination blob
        CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

        // Copy Blob
        try
        {
        using (var stream = await blob.OpenReadAsync()) 
        {            
            await destinationBlob.UploadFromStreamAsync(stream);          
        }

        log.Info("Copy Complete.");

        var assetFile = asset.AssetFiles.Create(blob.Name);
        assetFile.ContentFileSize = blob.Properties.Length;
        assetFile.IsPrimary = true;
        assetFile.Update();
        asset.Update();
        }
        catch (Exception ex)
        {
        log.Error(ex.Message);
        log.Info (ex.StackTrace);
        log.Info ("Copy Failed.");
        throw;
        }

        destinationLocator.Delete();
        writePolicy.Delete();

        return asset;
    }
##<a name="test-your-function"></a>测试函数

若要测试函数，需将 MP4 文件上传到连接字符串中所指定存储帐户的**输入**容器中。  

## <a name="next-step"></a>后续步骤

现在，你可以开始开发媒体服务应用程序了。 
 
若要更详细了解如何结合使用 Azure 媒体服务以及 Azure Functions 和逻辑应用来创建自定义内容创建工作流，并了解其完整示例/解决方案，请参阅 [GitHub 上的媒体服务 .NET 函数集成示例](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

另请参阅[使用 Azure WebHook 通过 .NET 监视媒体服务作业通知 ](media-services-dotnet-check-job-progress-with-webhooks.md)。 

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


