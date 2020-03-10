---
title: 使用 Azure 媒体服务实现故障转移流式处理 | Microsoft 文档
description: 本文介绍如何使用 Azure 媒体服务实现故障转移流式处理方案。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ae1371a8f025fd5e5722d483323fbe937538eb15
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78939213"
---
# <a name="implement-failover-streaming-with-media-services-v2"></a>通过媒体服务 v2 实现故障转移流式处理

本演练演示如何将内容 (Blob) 从一个资产复制到另一个资产，以便处理按需流式处理的冗余。 如果想要将 Azure 内容分发网络设置为当某个数据中心发生中断时在两个数据中心之间故障转移，则很适合采用此方案。 本演练使用 Azure 媒体服务 SDK、Azure 媒体服务 REST API 和 Azure 存储 SDK 来演示以下任务：

1. 在“数据中心 A”中设置一个媒体服务帐户。
2. 将一个夹层文件上传到源资产中。
3. 将该资产编码成多比特率 MP4 文件。 
4. 创建一个只读的共享访问签名定位符。 源资产可以使用此定位符获取对关联到源资产的存储帐户中的容器的读取访问权限。
5. 从上一步骤中创建的只读共享访问签名定位符获取源资产的容器名称。 在存储帐户之间复制 Blob 时必须使用此名称（本主题稍后会介绍。）
6. 为通过编码任务创建的资产创建源定位器。 

然后，要处理故障转移，请执行以下操作：

1. 在“数据中心 B”中设置一个媒体服务帐户。
2. 在目标媒体服务帐户中创建一个目标空资产。
3. 创建一个写入共享访问签名定位符。 目标空资产将使用此定位符获取对关联到目标资产的目标存储帐户中的容器的写入访问权限。
4. 使用 Azure 存储 SDK 在“数据中心 A”中的源存储帐户与“数据中心 B”中的目标存储帐户之间复制 Blob（资产文件）。 这些存储帐户与所需的资产关联。
5. 将复制到目标 blob 容器的 blob（资产文件）与目标资产关联。 
6. 在“数据中心 B”中为资产创建来源定位符，并指定针对“数据中心 A”中的资产生成的定位符 ID。

这样，便会提供 URL 的相对路径相同（只有基本 URL 不同）的流式处理 URL。 

然后，若要处理任何中断情况，可在这些源定位符的顶层创建内容分发网络。 

请注意以下事项：

* 当前版本的媒体服务 SDK 不支持以编程方式生成会将资产与资产文件关联的 IAssetFile 信息。 应该使用 CreateFileInfos 媒体服务 REST API 来实现此目的。 
* 不支持使用存储加密资产 (AssetCreationOptions.StorageEncrypted) 进行复制（因为两个媒体服务帐户中的加密密钥不同）。 
* 若要使用动态打包，请确保要从中流式传输内容的流式处理终结点处于“正在运行”状态。

## <a name="prerequisites"></a>必备条件

* 在新的或现有的 Azure 订阅中拥有两个媒体服务帐户。 请参阅[如何创建媒体服务帐户](media-services-portal-create-account.md)。
* 操作系统：Windows 7、Windows 2008 R2 或 Windows 8。
* .NET Framework 4.5 或 .NET Framework 4。
* Visual Studio 2010 SP1 或更高版本（专业版、高级专业版、旗舰版或学习版）。

## <a name="set-up-your-project"></a>设置项目

在本部分，将要创建并设置一个 C# 控制台应用程序项目。

1. 使用 Visual Studio 创建包含 C# 控制台应用程序项目的新解决方案。 输入 **HandleRedundancyForOnDemandStreaming** 作为名称，并单击“确定”。
2. 在与 **HandleRedundancyForOnDemandStreaming.csproj** 项目文件相同的级别创建 **SupportFiles** 文件夹。 在 **SupportFiles** 文件夹下创建 **OutputFiles** 和 **MP4Files** 文件夹。 将一个 .mp4 文件复制到 **MP4Files** 文件夹。 （在此示例中，使用了**ignite**文件。） 
3. 使用**NuGet**添加对媒体服务相关 dll 的引用。 在**Visual Studio 主菜单**中，选择 "**工具**" > **NuGet 包管理器**" > **程序包管理器控制台**"。 在控制台窗口中键入 **Install-Package windowsazure.mediaservices**，并按 Enter。
4. 添加此项目所需的其他引用： "System.web" 和 "system.web"。
5. 将默认添加到 **Programs.cs** 文件中的 **using** 语句替换为以下语句：

```csharp
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Runtime.Serialization.Json;
```

## <a name="add-code-that-handles-redundancy-for-on-demand-streaming"></a>添加用于处理按需流式处理冗余的代码

在本部分，将创建用于处理冗余的功能。

1. 将以下类级字段添加到 Program 类。

    ```csharp
    private static readonly string storageNameTarget = "amsstorageacct2";
    private static readonly string storageKeyTarget = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000==";
    private static readonly string storageNameSource = "amsstorageacct1";
    private static readonly string storageKeySource = "00000000000000000000000000000000000000000000000000000000000000000000000000000000000000==";

    private static readonly string sourceApiServer = "https://amsacct1.restv2.westus2-2.media.azure.net/api/";
    private static readonly string targetApiServer = "https://amsacct2.restv2.eastus.media.azure.net/api/";
    private static string tokenSPSource = null;
    private static string tokenSPTarget = null;

    private static CloudMediaContext contextSource = null;
    private static CloudMediaContext contextTarget = null;

    // Base support files path.  Update this field to point to the base path  
    // for the local support files folder that you create. 
    private static readonly string SupportFiles = Path.GetFullPath(@"../..\SupportFiles");

    // Paths to support files (within the above base path). 
    private static readonly string SingleInputMp4Path = Path.GetFullPath(SupportFiles + @"\MP4Files\ignite.mp4");
    private static readonly string OutputFilesFolder = Path.GetFullPath(SupportFiles + @"\OutputFiles");
    ```
2. 请使用以下定义替换默认的 Main 方法定义。 下面定义了从 Main 调用的方法定义。
        
    ```csharp
    static void Main(string[] args)
    {
        AzureAdTokenCredentials tokenCredentialsForAcct1 =
         new AzureAdTokenCredentials("microsoft.onmicrosoft.com",
             new AzureAdClientSymmetricKey("00000000-0000-0000-0000-000000000000", "00000000000000000000000000000000"),
             AzureEnvironments.AzureCloudEnvironment);

        AzureAdTokenCredentials tokenCredentialsForAcct2 =
        new AzureAdTokenCredentials("microsoft.onmicrosoft.com",
             new AzureAdClientSymmetricKey("00000000-0000-0000-0000-000000000000", "00000000000000000000000000000000"),
             AzureEnvironments.AzureCloudEnvironment);

        var tokenProviderSource = new AzureAdTokenProvider(tokenCredentialsForAcct1);
        var tokenProviderTarget = new AzureAdTokenProvider(tokenCredentialsForAcct2);
        try
        {
        tokenSPSource = tokenProviderSource.GetAccessToken().Item1.ToString();
        tokenSPTarget = tokenProviderTarget.GetAccessToken().Item1.ToString();

        contextSource = new CloudMediaContext(new Uri(sourceApiServer), tokenProviderSource);
        contextTarget = new CloudMediaContext(new Uri(targetApiServer), tokenProviderTarget);


        IAsset assetSingleFile = CreateAssetAndUploadSingleFile(contextSource,
                         AssetCreationOptions.None,
                         SingleInputMp4Path);

         IJob job = CreateEncodingJob(contextSource, assetSingleFile);

        if (job.State != JobState.Error)
        {
            IAsset sourceOutputAsset = job.OutputMediaAssets[0];
            // Get the locator for Smooth Streaming
            var sourceOriginLocator = GetStreamingOriginLocator(contextSource, sourceOutputAsset);

            Console.WriteLine("Locator Id: {0}", sourceOriginLocator.Id);

            // 1.Create a read-only SAS locator for the source asset to have read access to the container in the source Storage account (associated with the source Media Services account)
            var readSasLocator = GetSasReadLocator(contextSource, sourceOutputAsset);

            // 2.Get the container name of the source asset from the read-only SAS locator created in the previous step
            string containerName = (new Uri(readSasLocator.Path)).Segments[1];

            // 3.Create a target empty asset in the target Media Services account
            var targetAsset = CreateTargetEmptyAsset(contextTarget, containerName);

            // 4.Create a write SAS locator for the target empty asset to have write access to the container in the target Storage account (associated with the target Media Services account)
            ILocator writeSasLocator = CreateSasWriteLocator(contextTarget, targetAsset);

            // Get asset container name.
            string targetContainerName = (new Uri(writeSasLocator.Path)).Segments[1];
            Console.WriteLine(targetContainerName);

            // 5.Copy the blobs in the source container (source asset) to the target container (target empty asset)
            CopyBlobsFromDifferentStorage(containerName, targetContainerName, storageNameSource, storageKeySource, storageNameTarget, storageKeyTarget);

            // 6.Use the CreateFileInfos Media Services REST API to automatically generate all the IAssetFile’s for the target asset. 
            //      This API call is not supported in the current Media Services SDK for .NET. 
            CreateFileInfos(targetApiServer, tokenSPTarget, targetAsset.Id);
            // Check if the AssetFiles are now  associated with the asset.
            Console.WriteLine("Asset files associated with the {0} asset:", targetAsset.Name);
            foreach (var af in targetAsset.AssetFiles)
            {
            Console.WriteLine(af.Name);
            }

            // 7.Copy the Origin locator of the source asset to the target asset by using the same Id
            var replicatedLocatorPath = CreateTargetOriginLocatorWithRest(contextSource, contextTarget, sourceOriginLocator.Id, targetAsset.Id);

            // Create a full URL to the manifest file. Use this for playback
            // in streaming media clients. 
            string originalUrlForClientStreaming = sourceOriginLocator.Path + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";

            Console.WriteLine("Original Locator Path: {0}\n", originalUrlForClientStreaming);

            string replicatedUrlForClientStreaming = replicatedLocatorPath + GetPrimaryFile(sourceOutputAsset).Name + "/manifest";

            Console.WriteLine("Replicated Locator Path: {0}", replicatedUrlForClientStreaming);

            readSasLocator.Delete();
            writeSasLocator.Delete();
        }

        }
        catch (Exception e)
        {
        Console.WriteLine("Exception:" + e.ToString());
        }
    }
    ```
3. 以下方法定义是从 Main 调用的。 有关每种方法的详细信息，请参阅注释。

    >[!NOTE]
    >不同媒体服务策略的策略数限制为 1,000,000 个（例如，对于 Locator 策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的天数和访问权限，应使用相同的策略 ID。 例如，对于需要长期保留使用的定位符，请使用相同的策略 ID（非上传策略）。 有关详细信息，请参阅[此主题](media-services-dotnet-manage-entities.md#limit-access-policies)。

    ```csharp
    public static IAsset CreateAssetAndUploadSingleFile(CloudMediaContext context,
                         AssetCreationOptions assetCreationOptions,
                         string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

        var asset = context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0}", assetFile.Name);

        return asset;
    }

    public static IJob CreateEncodingJob(CloudMediaContext context, IAsset asset)
    {
        // Declare a new job.
        IJob job = context.Jobs.Create("My encoding job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName(context,
                            "Media Encoder Standard");

        // Create a task with the encoding details, using a string preset.
        // In this case "Adaptive Streaming" preset is used.
        ITask task = job.Tasks.AddNew("My encoding task",
        processor,
        "Adaptive Streaming",
        TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        var outputAssetName = "OutputAsset_" + Guid.NewGuid();
        task.OutputAssets.AddNew(outputAssetName,
        AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-{JobId}.txt file 
        // in your output folder.
        LogJobDetails(context, job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // Get an updated job reference.
        job = context.Jobs.Where(j => j.Id == job.Id).FirstOrDefault();
        // Since we the output asset contains a set of Smooth Streaming files,
        // set the .ism file to be the primary file
        if (job.State != JobState.Error)
        SetPrimaryFile(job.OutputMediaAssets[0]);

        return job;
    }

    public static ILocator GetStreamingOriginLocator(CloudMediaContext context, IAsset assetToStream)
    {
        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        IAssetFile manifestFile = GetPrimaryFile(assetToStream);

        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            

        IAccessPolicy policy = context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = context.Locators.CreateLocator(LocatorType.OnDemandOrigin,
        assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

        // Return the locator. 
        return originLocator;
    }

    public static ILocator GetSasReadLocator(CloudMediaContext context, IAsset asset)
    {
        IAccessPolicy accessPolicy = context.AccessPolicies.Create("File Download Policy",
        TimeSpan.FromDays(30), AccessPermissions.Read);

        ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
        asset, accessPolicy);

        return sasLocator;
    }

    public static ILocator CreateSasWriteLocator(CloudMediaContext context, IAsset asset)
    {

        IAccessPolicy writePolicy = context.AccessPolicies.Create("Write Policy",
        TimeSpan.FromDays(30), AccessPermissions.Write);

        ILocator sasLocator = context.Locators.CreateLocator(LocatorType.Sas,
        asset, writePolicy);

        return sasLocator;
    }

    public static IAsset CreateTargetEmptyAsset(CloudMediaContext context, string containerName)
    {
        // Create a new asset.
        IAsset assetToBeProcessed = context.Assets.Create(containerName,
        AssetCreationOptions.None);

        return assetToBeProcessed;
    }

    public static string CreateTargetOriginLocatorWithRest(CloudMediaContext contextSource, CloudMediaContext contextTarget,  string locatorIdToReplicate, string targetAssetId)
    {
        string locatorNewPath = "";

        if (!string.IsNullOrEmpty(tokenSPTarget))
        {
        var asset = contextTarget.Assets.Where(a => a.Id == targetAssetId).FirstOrDefault();

        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
        var accessPolicy = contextTarget.AccessPolicies.Create("RestTest", TimeSpan.FromDays(100),
                                    AccessPermissions.Read);
        if (asset != null)
        {
            string redirectedServiceUri = null;

            var xmlResponse = CreateLocator(targetApiServer, out redirectedServiceUri, tokenSPTarget,
                                asset.Id, accessPolicy.Id,
                                (int)LocatorType.OnDemandOrigin,
                                DateTime.UtcNow.AddMinutes(-10), locatorIdToReplicate);

            Console.WriteLine("Redirected to: " + redirectedServiceUri);
            if (xmlResponse != null)
            {
            Console.WriteLine(String.Format("Locator Id: {0}",
                            xmlResponse.GetElementsByTagName("Id")[0].InnerText));
            Console.WriteLine(String.Format("Locator Path: {0}",
                xmlResponse.GetElementsByTagName("Path")[0].InnerText));

            locatorNewPath = xmlResponse.GetElementsByTagName("Path")[0].InnerText;
            }
        }
        }

        return locatorNewPath;
    }

    public static void SetPrimaryFile(IAsset asset)
    {
        var ismAssetFiles = asset.AssetFiles.ToList().
            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase));

        if (ismAssetFiles.Count() != 1)
        throw new ArgumentException("The asset should have only one, .ism file");

        ismAssetFiles.First().IsPrimary = true;
        ismAssetFiles.First().Update();
    }

    public static IAssetFile GetPrimaryFile(IAsset asset)
    {
        // Cast the reference to a true IAssetFile type. 
        IAssetFile theManifest = asset.AssetFiles.ToList().
            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).
            FirstOrDefault();

        return theManifest;
    }

    public static void CopyBlobsFromDifferentStorage(string sourceContainerName, string targetContainerName,
                        string srcAccountName, string srcAccountKey,
                        string destAccountName, string destAccountKey)
    {
        var srcAccount = new CloudStorageAccount(new StorageCredentials(srcAccountName, srcAccountKey), true);
        var destAccount = new CloudStorageAccount(new StorageCredentials(destAccountName, destAccountKey), true);

        var cloudBlobClient = srcAccount.CreateCloudBlobClient();
        var targetBlobClient = destAccount.CreateCloudBlobClient();

        var sourceContainer = cloudBlobClient.GetContainerReference(sourceContainerName);
        var targetContainer = targetBlobClient.GetContainerReference(targetContainerName);
        targetContainer.CreateIfNotExists();

        string blobToken = sourceContainer.GetSharedAccessSignature(new SharedAccessBlobPolicy()
        {
        // Specify the expiration time for the signature.
        SharedAccessExpiryTime = DateTime.Now.AddDays(1),
        // Specify the permissions granted by the signature.
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read
        });

        foreach (var sourceBlob in sourceContainer.ListBlobs())
        {
        string fileName = (sourceBlob as ICloudBlob).Name;
        var sourceCloudBlob = sourceContainer.GetBlockBlobReference(fileName);
        sourceCloudBlob.FetchAttributes();

        if (sourceCloudBlob.Properties.Length > 0)
        {
            // In Azure Media Services, the files are stored as block blobs. 
            // Page blobs are not supported by Azure Media Services.  
            var destinationBlob = targetContainer.GetBlockBlobReference(fileName);
            destinationBlob.StartCopy(new Uri(sourceBlob.Uri.AbsoluteUri + blobToken));

            while (true)
            {
            // The StartCopyFromBlob is an async operation, 
            // so we want to check if the copy operation is completed before proceeding. 
            // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
            destinationBlob.FetchAttributes();
            if (destinationBlob.CopyState.Status != CopyStatus.Pending)
            {
                break;
            }
            //It's still not completed. So wait for some time.
            System.Threading.Thread.Sleep(1000);
            }
        }

        Console.WriteLine(fileName);
        }

        Console.WriteLine("Done copying.");
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(CloudMediaContext context, string mediaProcessorName)
    {

        var processor = context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

    // This method is a handler for events that track job progress.   
    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);

        switch (e.CurrentState)
        {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(null, job.Id);
            break;
        default:
            break;
        }
    }

    private static void LogJobStop(CloudMediaContext context, string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        // Log job errors if they exist.  
        if (job.State == JobState.Error)
        {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
            builder.AppendLine("  Task Id: " + task.Id);
            builder.AppendLine("    Error Code: " + detail.Code);
            builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
        }
        builder.AppendLine("***************************\n");
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = OutputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    private static void LogJobDetails(CloudMediaContext context, string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();

        builder.AppendLine("\nJob ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());

        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobDetails-{JobId}.txt
        string outputFile = OutputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    // Replace ":" with "_" in Job id values so they can 
    // be used as log file names.  
    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }

    // Write method output to the output files folder.
    private static void WriteToFile(string outFilePath, string fileContent)
    {
        StreamWriter sr = File.CreateText(outFilePath);
        sr.WriteLine(fileContent);
        sr.Close();
    }

    //////////////////////////////////////////////////////
    /// The following methods use REST calls.
    //////////////////////////////////////////////////////
    public static XmlDocument CreateLocator(string mediaServicesApiServerUri,
                        out string redirectedMediaServicesApiServerUri,
                        string bearerToken, string assetId,
                        string accessPolicyId, int locatorType,
                        DateTime startTime, string locatorIdToReplicate = null,
                        bool autoRedirect = true)
    {
        if (string.IsNullOrEmpty(mediaServicesApiServerUri))
        {
        mediaServicesApiServerUri = "https://media.windows.net/api/";
        }
        if (!mediaServicesApiServerUri.EndsWith("/"))
        mediaServicesApiServerUri = mediaServicesApiServerUri + "/";

        if (string.IsNullOrEmpty(bearerToken)) throw new ArgumentNullException("BearerToken");
        if (string.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");
        if (string.IsNullOrEmpty(accessPolicyId)) throw new ArgumentNullException("accessPolicyId");

        redirectedMediaServicesApiServerUri = null;
        XmlDocument xmlResponse = null;

        StringBuilder sb = new StringBuilder();
        sb.Append("{ \"AssetId\" : \"" + assetId + "\"");
        sb.Append(", \"AccessPolicyId\" : \"" + accessPolicyId + "\"");
        sb.Append(", \"Type\" : \"" + locatorType + "\"");
        if (startTime != DateTime.MinValue)
        sb.Append(", \"StartTime\" : \"" + startTime.ToString("G", CultureInfo.CreateSpecificCulture("en-us")) + "\"");
        if (!string.IsNullOrEmpty(locatorIdToReplicate))
        sb.Append(", \"Id\" : \"" + locatorIdToReplicate + "\"");
        sb.Append("}");

        string requestbody = sb.ToString();

        try
        {
        var request = GenerateRequest("POST", mediaServicesApiServerUri, "Locators",
            null, bearerToken, requestbody);
        var response = (HttpWebResponse)request.GetResponse();

        switch (response.StatusCode)
        {
            case HttpStatusCode.MovedPermanently:
            //Recurse once with the mediaServicesApiServerUri redirect Location:
            if (autoRedirect)
            {
                redirectedMediaServicesApiServerUri = response.Headers["Location"];
                string secondRedirection = null;
                xmlResponse = CreateLocator(redirectedMediaServicesApiServerUri,
                            out secondRedirection, bearerToken,
                            assetId, accessPolicyId, locatorType,
                            startTime, locatorIdToReplicate, false);
            }
            else
            {
                Console.WriteLine("Redirection to {0} failed.",
                mediaServicesApiServerUri);
                return null;
            }
            break;
            case HttpStatusCode.Created:
            using (Stream responseStream = response.GetResponseStream())
            {
                using (StreamReader stream = new StreamReader(responseStream))
                {
                string responseString = stream.ReadToEnd();
                var reader = JsonReaderWriterFactory.
                    CreateJsonReader(Encoding.UTF8.GetBytes(responseString),
                    new XmlDictionaryReaderQuotas());

                xmlResponse = new XmlDocument();
                reader.Read();
                xmlResponse.LoadXml(reader.ReadInnerXml());
                }
            }
            break;

            default:
            Console.WriteLine(response.StatusDescription);
            break;
        }
        }
        catch (WebException ex)
        {
        Console.WriteLine(ex.Message);
        }

        return xmlResponse;
    }

    public static void CreateFileInfos(string mediaServicesApiServerUri,
                    string bearerToken,
                    string assetId
                    )
    {
        if (!mediaServicesApiServerUri.EndsWith("/"))
        mediaServicesApiServerUri = mediaServicesApiServerUri + "/";

        if (String.IsNullOrEmpty(bearerToken)) throw new ArgumentNullException("bearerToken");
        if (String.IsNullOrEmpty(assetId)) throw new ArgumentNullException("assetId");

        try
        {
        var request = GenerateRequest("GET", mediaServicesApiServerUri, "CreateFileInfos",
            String.Format(CultureInfo.InvariantCulture, "assetid='{0}'", assetId), bearerToken, null);

        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            if (response.StatusCode == HttpStatusCode.MovedPermanently)
            {
            string redirectedMediaServicesApiUrl = response.Headers["Location"];

            CreateFileInfos(redirectedMediaServicesApiUrl, bearerToken, assetId);
            }
            else if ((response.StatusCode != HttpStatusCode.OK) &&
            (response.StatusCode != HttpStatusCode.Accepted) &&
            (response.StatusCode != HttpStatusCode.Created) &&
            (response.StatusCode != HttpStatusCode.NoContent))
            {
            throw new Exception("Invalid response received ");
            }
        }
        }
        catch (WebException ex)
        {
        Console.WriteLine(ex.Message);
        }
    }

    private static HttpWebRequest GenerateRequest(string verb,
                            string mediaServicesApiServerUri,
                            string resourcePath, string query,
                            string bearerToken, string requestbody)
    {
        var uriBuilder = new UriBuilder(mediaServicesApiServerUri);
        uriBuilder.Path += resourcePath;
        if (query != null)
        {
        uriBuilder.Query = query;
        }

        HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uriBuilder.Uri);
        request.AllowAutoRedirect = false; //We manage our own redirects.
        request.Method = verb;

        if (resourcePath == "$metadata")
        request.MediaType = "application/xml";
        else
        {
        request.ContentType = "application/json;odata=verbose";
        request.Accept = "application/json;odata=verbose";
        }

        request.Headers.Add("DataServiceVersion", "3.0");
        request.Headers.Add("MaxDataServiceVersion", "3.0");
        request.Headers.Add("x-ms-version", "2.19");
        request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + bearerToken);

        if (requestbody != null)
        {
        var requestBytes = Encoding.ASCII.GetBytes(requestbody);
        request.ContentLength = requestBytes.Length;

        var requestStream = request.GetRequestStream();
        requestStream.Write(requestBytes, 0, requestBytes.Length);
        requestStream.Close();
        }
        else
        {
        request.ContentLength = 0;
        }
        return request;
    }
    ```
    
## <a name="content-protection"></a>内容保护

本主题中的示例演示了清除流式处理。 如果要执行受保护的流式处理，需要设置其他一些功能，需要使用相同的**AssetDeliveryPolicy**、相同的**ContentKeyAuthorizationPolicy**或外部密钥服务器 URL，并且需要复制具有相同标识符的内容密钥。

有关内容保护的详细信息，请参阅[使用 AES-128 动态加密和密钥传送服务](media-services-protect-with-aes128.md)。

## <a name="see-also"></a>另请参阅

[使用 Azure Webhook 监视媒体服务作业通知](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="next-steps"></a>后续步骤

现在，可以使用流量管理器在两个数据中心之间路由请求，因此可在发生任何中断时故障转移。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

