---
title: "使用 Azure Media Video Thumbnails 创建视频摘要 | Microsoft Docs"
description: "视频摘要可通过自动选择来自源视频的有趣片段帮助你创建长视频的摘要。 当你要提供有关长视频内容的快速概述时，这很有用。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/26/2016
ms.author: milanga;juliako;
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c6f4a020430f464a97b0716dea2a40719c0b064


---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>使用 Azure 媒体视频缩略图创建视频摘要
## <a name="overview"></a>概述
通过 **Azure Media Video Thumbnails** 媒体处理器 (MP)，可创建视频摘要，这对于要预览长视频摘要的客户来说很有用。 例如，当客户将鼠标悬停在缩略图上时，他们可能希望看到一小段“摘要视频”。 通过配置预设值稍稍调整 **Azure Media Video Thumbnails** 的参数，即可使用 MP 的强大快照检测和串联技术，以算法形式生成描述性子剪辑。  

**Azure Media Video Thumbnail** MP 目前以预览版提供。

此主题提供有关 **Azure Media Video Thumbnail** 的详细信息，并演示如何将它与适用于 .NET 的媒体服务 SDK 配合使用

## <a name="video-summary-example"></a>视频摘要示例
下面是 Azure 媒体视频缩略图媒体处理器可以执行的操作的一些示例：

### <a name="original-video"></a>原始视频
[原始视频](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>视频缩略图结果
[视频缩略图结果](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>任务配置（预设）
使用 **Azure Media Video Thumbnails** 创建视频缩略图任务时，必须指定配置预设值。 以上缩略图示例使用以下 JSON 基本配置创建：

    {"version":"1.0"}

当前你可更改以下参数：

| Param | 说明 |
| --- | --- |
| outputAudio |指定生成的视频是否包含音频。 <br/>允许的值为：True 或 False。 默认值为 True。 |
| fadeInFadeOut |指定单独动态缩略图之间是否使用淡入淡出转换。  <br/>允许的值为：True 或 False。  默认值为 True。 |
| maxMotionThumbnailDurationInSecs |指定生成的整个视频的时长的整数。  默认值取决于原始视频的持续时间。 |

下表描述了未使用 **maxMotionThumbnailInSecs** 时的默认持续时间。

|  |  |  |
| --- | --- | --- | --- | --- |
| 视频持续时间 |d < 3 分钟 |3 分钟 < d < 15 分钟 |
| 缩略图持续时间 |15 秒（2-3 个场景） |30 秒（3-5 个场景） |

下面的 JSON 设置可用的参数。

    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>代码示例
以下程序演示如何：

1. 创建资产并将媒体文件上传到资产。
2. 使用基于包含以下 json 预设值的配置文件的视频缩略图任务，创建一个作业。 
   
        {                
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }
3. 下载输出文件。 

### <a name="net-code"></a>.NET 代码
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;

            static void Main(string[] args)
            {

                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
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

        }
    }

### <a name="video-thumbnail-output"></a>视频缩略图输出
[视频缩略图输出](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相关链接
[Azure 媒体服务分析概述](media-services-analytics-overview.md)

[Azure 媒体分析演示](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)




<!--HONumber=Nov16_HO3-->


