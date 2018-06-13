---
title: 使用 Azure 媒体分析检测动作 | Microsoft Docs
description: Azure 媒体动作检测器媒体处理器 (MP) 可让你在冗长且平淡的视频中有效识别出你感兴趣的部分。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 8488b968fe2ab823479d70a98ba86be97b28f67d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783616"
---
# <a name="detect-motions-with-azure-media-analytics"></a>使用 Azure 媒体分析检测动作
## <a name="overview"></a>概述
借助 **Azure Media Motion Detector** 媒体处理器 (MP)，用户可在冗长且平淡的视频中有效识别出感兴趣的部分。 可以对静态相机数据片段使用动作检测，以识别视频中有动作的部分。 它会生成 JSON 文件，其中包含带时间戳的元数据，以及发生事件的边界区域。

此技术面向安全视频提要，它可以将动作分类为相关事件和误报（例如阴影或光源变化）。 这样，便可以在不会被发送无止境的不相关事件的情况下，从相机源生成安全警报，同时能够从长时间的监控视频中提取感兴趣的片段。

**Azure Media Motion Detector** MP 目前以预览版提供。

本文提供有关 **Azure Media Motion Detector** 的详细信息，并演示如何通过适用于 .NET 的媒体服务 SDK 使用它

## <a name="motion-detector-input-files"></a>动作检测器输入文件
视频文件。 目前支持以下格式：MP4、MOV 和 WMV。

## <a name="task-configuration-preset"></a>任务配置（预设）
在使用 **Azure Media Motion Detector**创建任务时，必须指定配置预设。 

### <a name="parameters"></a>parameters
可以使用以下参数：

| 名称 | 选项 | 说明 | 默认 |
| --- | --- | --- | --- |
| sensitivityLevel |字符串：'low'、'medium'、'high' |设置报告动作情况的敏感度级别。 调整此项是为了调整误报数量。 |'medium' |
| frameSamplingValue |正整数 |设置算法的运行频率。 1 等于每个帧，2 是指每 2 个帧，如此类推。 |1 |
| detectLightChange |布尔值：'true'、'false' |设置是否在结果中报告轻微的更改 |'False' |
| mergeTimeThreshold |Xs-time: Hh:mm:ss<br/>示例：00:00:03 |指定动作事件之间的时间窗口，其中的 2 个事件将组合成 1 个事件进行报告。 |00:00:00 |
| detectionZones |检测区域的一个数组：<br/>- 检测区域是一个包含 3 个或 3 个以上点的数组<br/>- 点是从 0 到 1 的 x 和 y 坐标。 |描述要使用的多边形检测区域列表。<br/>报告结果时还将报告以 ID 表示的区域，其中第一个是 ‘id’:0 |单个区域，涵盖整个帧。 |

### <a name="json-example"></a>JSON 示例

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>动作检测器输出文件
动作检测作业会在输出资产中返回 JSON 文件，该文件描述视频中的动作警报及其类别。 该文件将包含有关在视频中检测到的动作的时间和持续时间的信息。

一旦固定背景视频（例如监控视频）中出现运动对象，动作检测器 API 将提供指示器。 动作检测器经过训练可减少误报（例如光源和阴影变化）。 当前算法限制包括夜视视频、半透明对象和小对象。

### <a id="output_elements"></a>输出 JSON 文件的元素
> [!NOTE]
> 在最新版本中，输出 JSON 格式已更改，对某些客户来说可以说是重大更改。
> 
> 

下表描述了输出 JSON 文件的元素。

| 元素 | 说明 |
| --- | --- |
| 版本 |这是指视频 API 的版本。 当前版本为 2。 |
| 时间刻度 |视频每秒的“刻度”数。 |
| Offset |时间戳的时间偏移量（以“刻度”为单位）。 在版本 1.0 的视频 API 中，此属性始终为 0。 在我们将来支持的方案中，此值可能会更改。 |
| Framerate |视频的每秒帧数。 |
| Width, Height |表示视频的宽度和高度（以像素为单位）。 |
| 开始 |开始时间戳（以“刻度”为单位）。 |
| Duration |事件的长度（以“刻度”为单位）。 |
| 时间间隔 |事件中每个条目的间隔（以“刻度”为单位）。 |
| 活动 |每个事件片段包含在该持续时间内检测到的动作。 |
| Type |在当前版本中，对于一般动作，该属性始终为“2”。 此标签可让视频 API 在将来的版本中灵活地为动作分类。 |
| RegionID |如上所述，在此版本中此属性始终为 0。 此标签可让视频 API 在将来的版本中灵活地查找各区域中的动作。 |
| 区域 |表示你关注的动作在视频中的区域。 <br/><br/>-“id”表示区域面积 - 且在此版本中只有一个，ID 0。 <br/>#NAME? 目前支持“rectangle”和“polygon”。<br/> 如果指定了 "rectangle"，则区域具有以 X、Y、宽度及高度表示的维。 X 和 Y 坐标表示规范化 0.0 到 1.0 比例中的区域的左上角 XY 坐标。 宽度和高度表示规范化 0.0 到 1.0 比例中的区域的大小。 在当前版本中，X、Y、宽度和高度始终固定为 0、0、1、1。 <br/>如果指定了“polygon”，则区域的维度以点来表示。 <br/> |
| Fragments |元数据划分成称为“片段”的不同段。 每个片段包含开始时间、持续时间、间隔数字和事件。 没有事件的片段表示在该开始时间和持续时间内没有检测到任何动作。 |
| 括号 [] |每个括号表示事件中的单个间隔。 如果该间隔显示空括号，则表示没有检测到动作。 |
| 位置 |事件下的此新项列出发生动作的位置。 这比检测区域更具体。 |

以下 JSON 示例显示输出：

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>限制
* 支持的输入视频格式包括 MP4、MOV 和 WMV。
* 动作检测已针对固定背景视频优化。 算法专注于降低误报，例如光源变化和阴影。
* 某些动作可能因技术难题而无法检测到，例如夜视视频、半透明对象和小对象。

## <a name="net-sample-code"></a>.NET 示例代码

以下程序演示如何：

1. 创建资产并将媒体文件上传到资产。
2. 基于包含以下 json 预设的配置文件，使用视频动作检测任务创建作业： 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
            }
    ```

3. 下载输出 JSON 文件。 

#### <a name="create-and-configure-a-visual-studio-project"></a>创建和配置 Visual Studio 项目

设置开发环境，并根据[使用 .NET 进行媒体服务开发](media-services-dotnet-how-to-use.md)中所述，在 app.config 文件中填充连接信息。 

#### <a name="example"></a>示例

```csharp

using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace VideoMotionDetection
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);

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
```

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相关链接
[Azure 媒体服务动作检测器博客](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure 媒体服务分析概述](media-services-analytics-overview.md)

[Azure 媒体分析演示](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

