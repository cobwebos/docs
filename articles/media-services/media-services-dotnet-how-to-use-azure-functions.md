---
title: "开发使用媒体服务的 Azure Functions"
description: "本主题说明如何在 Azure 门户中开始开发使用媒体服务的 Azure Functions。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: juliako
ms.openlocfilehash: e8cad53d95186f4f7679d1f19f339ad4149059a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="develop-azure-functions-with-media-services"></a>开发使用媒体服务的 Azure Functions

本主题说明如何开始创建使用媒体服务的 Azure Functions。 本主题中定义的 Azure Function 可监视新 MP4 文件中名为**输入**的存储帐户容器。 将文件放入存储容器后，blob 触发器就会执行此函数。 要查看 Azure 函数，请参阅 Azure 函数部分的[概述](../azure-functions/functions-overview.md)和其他主题。

如果你想要浏览并部署使用 Azure Media Services 的现有 Azure 功能，请查看[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)。 此存储库包含几个示例，示例中将使用媒体服务来演示有关直接从 Blob 存储引入内容、编码以及将内容写回 Blob 存储的工作流。 此存储库还包含演示如何通过 WebHook 和 Azure 队列监视作业通知的示例。 也可根据[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) 存储库中的示例进行 Functions 开发。 若要部署此函数，请按“部署到 Azure”按钮。

## <a name="prerequisites"></a>先决条件

- 必须先具有有效的 Azure 帐户，然后才能创建第一个函数。 如果还没有 Azure 帐户， [可以使用免费帐户](https://azure.microsoft.com/free/)。
- 若要创建针对 Azure 媒体服务 (AMS) 帐户执行操作或者侦听媒体服务发送的事件的 Azure Functions，应该根据[此文](media-services-portal-create-account.md)中所述创建一个 AMS 帐户。
    
## <a name="create-a-function-app"></a>创建函数应用

1. 转到 [Azure 门户](http://portal.azure.com)，然后使用 Azure 帐户登录。
2. 根据[此文](../azure-functions/functions-create-function-app-portal.md)中所述创建 Function App。

>[!NOTE]
> **StorageConnection** 环境变量中指定的存储帐户需与应用位于同一区域（详见下一步）。

## <a name="configure-function-app-settings"></a>配置 Function App 设置

开发媒体服务函数时，可随时添加要在整个函数中使用的环境变量。 若要配置应用设置，请单击“配置应用设置”链接。 有关详细信息，请参阅[如何配置 Azure Function App 设置](../azure-functions/functions-how-to-use-azure-function-app-settings.md)。 

本文中定义的函数假定应用设置中具备以下环境变量：

**AMSAADTenantDomain**：Azure AD 租户终结点。 有关连接到 AMS API 的详细信息，请参阅[此文章](media-services-use-aad-auth-to-access-ams-api.md)。

**AMSRESTAPIEndpoint**：表示 REST API 终结点的 URI。 

**AMSClientId**：Azure AD 应用程序客户端 ID。

**AMSClientSecret**：Azure AD 应用程序客户端密码。

**StorageConnection**：与媒体服务帐户关联的帐户的存储连接。 “function.json”文件和“run.csx”文件使用了此值（如下所述）。

## <a name="create-a-function"></a>创建函数

部署 Function App 后，可在**应用服务** Azure Functions 中找到它。

1. 选择 Function App，然后单击“新建函数”。
2. 选择“C#”语言和“数据处理”方案。
3. 选择“BlobTrigger”模板。 只要将 blob 上传到**输入**容器，就会触发此函数。 下一步的“路径”中指定了**输入**名称。

    ![文件](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. 选择“BlobTrigger”后，页面上会显示更多控件。

    ![文件](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. 单击“创建” 。 

## <a name="files"></a>文件

Azure 函数与代码文件以及本部分所述的其他文件相关联。 当使用 Azure 门户创建函数时，将为你创建 function.json 和 run.csx。 需要添加或上传 project.json 文件。 本文剩余部分对每个文件进行了简要介绍，并说明其定义。

![文件](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Function.json 文件定义函数绑定和其他配置设置。 运行时使用此文件确定要监视的事件，以及如何将数据传入函数执行和从函数执行返回数据。 有关详细信息，请参阅 [Azure Functions HTTP 和 webhook 绑定](../azure-functions/functions-reference.md#function-code)。

>[!NOTE]
>将 **disabled** 属性设置为“true”，阻止函数执行。 

将现有 function.json 文件的内容替换为以下代码：

```
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

project.json 文件包含依赖项。 下面是一个 **project.json** 文件示例，其中包含 Nuget 所需的 .NET Azure 媒体服务包。 请注意，版本号将随包的最新更新而改变，因此应确认最新版本。 

向 project.json 添加以下定义。 

```
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

这是函数的 C# 代码。  下方定义的函数可监视新 MP4 文件中名为**输入**的存储帐户容器（即路径中指定的容器）。 将文件放入存储容器后，blob 触发器就会执行此函数。
    
本部分定义的示例演示了 

1. 如何将资产引入媒体服务帐户（通过将 blob 复制到 AMS 资产），以及 
2. 如何提交使用 Media Encoder Standard“自适应流式处理”预设的编码作业。

在实际方案中，很可能需要跟踪作业进度，并发布编码的资产。 有关详细信息，请参阅[使用 Azure WebHook 监视媒体服务作业通知](media-services-dotnet-check-job-progress-with-webhooks.md)。 有关更多示例，请参阅[媒体服务 Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)。  

将现有 run.csx 文件的内容替换为以下代码。 定义后，请单击“保存并运行”。

```
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

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
```

##<a name="test-your-function"></a>测试函数

要测试函数，需将 MP4 文件上传到连接字符串中所指定存储帐户的**输入**容器中。  

1. 选择在 StorageConnection 环境变量中指定的存储帐户。
2. 单击“Blob”。
3. 单击“+ 容器”。 将容器命名为 input。
4. 按“上传”并浏览到要上传的 .mp4 文件。

>[!NOTE]
> 在消耗计划中使用 Blob 触发器时，函数应用处于空闲状态后，处理新 Blob 的过程中可能会出现长达 10 分钟的延迟。 函数应用运行后，就会立即处理 Blob。 有关详细信息，请参阅 [Blob 存储触发器和绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob#blob-storage-triggers-and-bindings)。

## <a name="next-steps"></a>后续步骤

现在，可以开始开发媒体服务应用程序了。 
 
若要更详细了解如何结合使用 Azure 媒体服务以及 Azure Functions 和逻辑应用来创建自定义内容创建工作流，并了解其完整示例/解决方案，请参阅 [GitHub 上的媒体服务 .NET 函数集成示例](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

另请参阅[使用 Azure WebHook 通过 .NET 监视媒体服务作业通知 ](media-services-dotnet-check-job-progress-with-webhooks.md)。 

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

