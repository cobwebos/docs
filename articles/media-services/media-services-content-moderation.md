---
title: "使用 Azure 媒体内容审查器检测可能的成人和猥亵内容 | Microsoft Docs"
description: "视频审查可以帮助检测视频中可能存在的成人和猥亵内容。"
services: media-services
documentationcenter: 
author: sanjeev3
manager: mikemcca
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: 43e22e553b5243d6edc413c7a667089793f95396
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>使用 Azure 媒体内容审查器检测可能的成人和猥亵内容

## <a name="overview"></a>概述
借助 **Azure 媒体内容审查器**媒体处理器 (MP)，你可以对视频使用机器辅助审查。 例如，你可能希望检测视频中可能存在的成人和猥亵内容，以及复查由人工审查团队标记出的内容。

**Azure 媒体内容审查器** MP 目前为预览版。

本文提供了有关 **Azure 媒体内容审查器**的详细信息，并展示了如何通过适用于 .NET 的媒体服务 SDK 使用它。

## <a name="content-moderator-input-files"></a>内容审查器输入文件
视频文件。 目前支持以下格式：MP4、MOV 和 WMV。

## <a name="content-moderator-output-files"></a>内容审查器输出文件
采用 JSON 格式的审查输出包括自动检测到的快照和关键帧。 关键帧将返回，并且将带有可信度分数以指明是成人或猥亵内容的可能性。 它们还包括一个布尔值标志，指明是否建议进行复查。 复查建议标志是基于针对成人和猥亵分数的内部阈值分配的值。

## <a name="elements-of-the-output-json-file"></a>输出 JSON 文件中的元素

作业将生成一个 JSON 输出文件，其中包含有关检测到的快照以及它们是否包含成人或猥亵内容的元数据。

输出 JSON 包含以下元素：

### <a name="root-json-elements"></a>根 JSON 元素

| 元素 | 说明 |
| --- | --- |
| version |内容审查器的版本。 |
| timescale |视频每秒的“刻度”数。 |
| offset |时间戳的时间偏移量。 在版本 1.0 的视频 API 中，此值将始终为 0。 此值在将来可能会更改。 |
| framerate |视频的每秒帧数。 |
| width |输出视频帧的宽度，以像素为单位。|
| height |输出视频帧的高度，以像素为单位。|
| totalDuration |输入视频的持续时间，以“时钟周期”为单位。 |
| [fragments](#fragments-json-elements) |元数据划分成称为“片段”的不同段。 每个片段是一个自动检测到的快照，包含开始时间、持续时间、间隔数字和事件。 |

### <a name="fragments-json-elements"></a>片段 JSON 元素

|元素|说明|
|---|---|
| start |第一个事件的开始时间（以“刻度”为单位）。 |
| duration |片段的长度（以“刻度”为单位）。 |
| interval |片段中每个事件条目的间隔（以“刻度”为单位）。 |
| [events](#events-json-elements) |每个事件都代表一个剪辑，每个剪辑包含在该持续时间内检测到和跟踪的关键帧。 它是事件的数组。 外部数组代表一个时间间隔。 内部数组包含在该时间点发生的 0 个或多个事件。|

### <a name="events-json-elements"></a>事件 JSON 元素

|元素|说明|
|---|---|
| reviewRecommended | `true` 或 `false`，具体取决于 **adultScore** 或 **racyScore** 是否超出了内部阈值。 |
| adultScore | 可能的成人内容的可信度分数，范围为 0.00 到 0.99。 |
| racyScore | 可能的猥亵内容的可信度分数，范围为 0.00 到 0.99。 |
| index | 帧的索引，处于从第一个帧索引到最后一个帧索引这一范围内。 |
| timestamp | 帧的位置，以“刻度”为单位。 |
| shotIndex | 父快照的索引。 |


## <a name="content-moderation-quickstart-and-sample-output"></a>内容审查快速入门和示例输出

### <a name="task-configuration-preset"></a>任务配置（预设）
在使用 **Azure 媒体内容审查器**创建任务时，必须指定配置预设。 以下配置预设仅适用于内容审查。

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET 代码示例

以下 .NET 代码示例使用媒体服务 .NET SDK 运行内容审查器作业。 它采用媒体服务资产作为输入，其中包含要审查的视频。
有关完整的源代码和 Visual Studio 项目，请参阅[内容审查器视频快速入门](../cognitive-services/Content-Moderator/video-moderation-api.md)。


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

有关完整的源代码和 Visual Studio 项目，请查看[内容审查器视频快速入门](../cognitive-services/Content-Moderator/video-moderation-api.md)。

### <a name="json-output"></a>JSON 输出

以下内容审查器 JSON 输出示例已被截断。

> [!NOTE]
> 以秒数表示的关键帧位置 = 时间戳/时间刻度

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


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相关链接
[Azure 媒体服务分析概述](media-services-analytics-overview.md)

[Azure 媒体分析演示](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>后续步骤

详细了解内容审查器的[视频审查和复查解决方案](../cognitive-services/Content-Moderator/video-moderation-human-review.md)。

从[视频审查快速入门](../cognitive-services/Content-Moderator/video-moderation-api.md)获取完整的源代码和 Visual Studio 项目。 

了解如何采用 .NET 基于审查输出和[审查脚本](../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md)生成[视频复查](../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md)。

查看详细的 .NET [视频审查和复查教程](../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md)。 
