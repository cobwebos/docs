---
title: "通过媒体编码器高级工作流进行高级编码 | Microsoft Docs"
description: "了解如何使用媒体编码器高级工作流进行编码。 代码示例用 C# 编写且使用适用于 .NET 的媒体服务 SDK。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 0f4c87ac-810a-4d42-8df8-923dff2016c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: juliako
ms.openlocfilehash: 2b03853bf07e05c07fd730d5e8a8563963887921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>使用媒体编码器高级工作流进行高级编码
> [!NOTE]
> 本主题中所述的媒体编码器高级工作流媒体处理器在中国不可用。
>
>

有关高级编码器的问题，请发送电子邮件到 Microsoft.com。

## <a name="overview"></a>概述
Microsoft Azure 媒体服务即将推出**媒体编码器高级工作流**媒体处理器。 此处理器针对高级按需工作流提供高级编码功能。

以下主题概述了与**媒体编码器高级工作流**相关的详细信息：

* [媒体编码器高级工作流支持的格式](media-services-premium-workflow-encoder-formats.md) - 介绍**媒体编码器高级工作流**支持的文件格式和编解码器。
* [Azure 点播媒体编码器的概述和比较](media-services-encode-asset.md)比较了**媒体编码器高级工作流**和 **Media Encoder Standard** 的编码功能。

本主题演示如何在**媒体编码器高级工作流**中使用 .NET 进行编码。

**媒体编码器高级工作流**的编码任务需要一个名为“Workflow”文件的单独配置文件。 这些文件的扩展名为 .workflow，由[工作流设计器](media-services-workflow-designer.md)工具创建。

也可以从[此处](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)获取默认的工作流文件。 该文件夹还包含这些文件的相关说明。

需要将工作流文件作为资产上传到媒体服务帐户，并且应将此资产传递给编码任务。

## <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 

## <a name="encoding-example"></a>编码示例

以下示例演示了如何使用**媒体编码器高级工作流**进行编码。

将执行以下步骤：

1. 创建资产并上传工作流文件。
2. 创建资产并上传源媒体文件。
3. 获取“Media Encoder Premium Workflow”媒体处理器。
4. 创建作业和任务。

    在大多数情况下，该任务的配置字符串为空（如以下示例中所示）。 在某些高级方案中（要求动态设置运行时属性），需要为编码任务提供 XML 字符串。 此类方案的示例包括：创建一个覆盖层、并行或依序媒体拼接、字幕。
5. 向该任务添加输入资产。

    1. 第 1 个 - 工作流资产。
    2. 第二个 - 视频资产。

    >[!NOTE]
    >必须将工作流资产添加到媒体资产前面的任务。
   此任务的配置字符串应为空。
   
6. 提交编码作业。

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;

        namespace MediaEncoderPremiumWorkflowSample
        {
            class Program
            {
                private static readonly string _AADTenantDomain =
                    ConfigurationManager.AppSettings["AADTenantDomain"];
                private static readonly string _RESTAPIEndpoint =
                    ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

                // Field for service context.
                private static CloudMediaContext _context = null;

                private static readonly string _supportFiles =
                    Path.GetFullPath(@"../..\Media");

                private static readonly string _workflowFilePath =
                    Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

                private static readonly string _singleMP4InputFilePath =
                    Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

                static void Main(string[] args)
                {
                    var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
                    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
                    var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
                    IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

                }

                static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
                {
                    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
                    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                    var fileName = Path.GetFileName(singleFilePath);

                    var assetFile = asset.AssetFiles.Create(fileName);

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    Console.WriteLine("Upload {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    return asset;
                }

                static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Premium Workflow encoding job");
                    // Get a media processor reference, and pass to it the name of the
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                        processor,
                        "",
                        TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(workflow);
                    task.InputAssets.Add(video); // we add one asset
                                                 // Add an output asset to contain the results of the job.
                                                 // This output is specified as AssetCreationOptions.None, which
                                                 // means the output asset is not encrypted.
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.None);

                    // Use the following event handler to check job progress.  
                    job.StateChanged += new
                            EventHandler<JobStateChangedEventArgs>(StateChanged);

                    // Launch the job.
                    job.Submit();

                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                    progressJobTask.Wait();

                    // If job state is Error the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        throw new Exception("\nExiting method due to job error.");
                    }

                    return job.OutputMediaAssets[0];
                }

                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);

                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
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
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
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
            }
        }

有关高级编码器的问题，请发送电子邮件到 Microsoft.com。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
