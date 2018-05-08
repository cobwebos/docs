---
title: Hyperlapse 媒体文件与 Azure Media Hyperlapse | Microsoft Docs
description: Azure Media Hyperlapse 可以使用第一人称视角或运动相机内容创建流畅缩时视频。 本主题说明如何使用媒体索引器。
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2018
ms.author: adsolank
ms.openlocfilehash: ed64a616538ed4699abc03225a2dcf27d164521f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Hyperlapse 媒体文件与 Azure Media Hyperlapse
Azure Media Hyperlapse 是可以使用第一人称视角或运动相机内容创建流畅缩时视频的媒体处理器 (MP)。  Azure 媒体服务的基于云的 Microsoft Hyperlapse 与 [Microsoft Research 的桌面 Hyperlapse Pro 和手机版 Hyperlapse Mobile](http://aka.ms/hyperlapse) 相似，它运用大规模的 Azure 媒体服务媒体处理平台来实现水平缩放，以及并行化批量 Hyperlapse 处理。

> [!IMPORTANT]
> Microsoft Hyperlapse 最适合用于通过移动相机拍摄第一人称视角内容。 尽管在静态相机中也能运行，但 Azure 媒体 Hyperlapse 媒体处理器无法保证其他类型内容的性能及质量。
> 
> 

Azure Media Hyperlapse 作业接受输入 MP4、MOV 或 WMV 资产文件以及配置文件，以指定视频中要缩时的帧及其速度（例如前 10,000 帧的速度为 2x）。  输出是输入视频经过稳定和缩时转译的结果。

## <a name="hyperlapse-an-asset"></a>将资产进行 Hyperlapse 处理
首先，请将所需的输入文件上传到 Azure 媒体服务。  若要深入了解有关上传和管理内容的概念，请阅读[内容管理文章](media-services-portal-vod-get-started.md)。

### <a id="configuration"></a>Hyperlapse 的配置预设
将内容上传到媒体服务帐户后，需要构造配置预设。  下表说明了用户指定的字段：

| 字段 | 说明 |
| --- | --- |
| StartFrame |开始 Microsoft Hyperlapse 处理时所在的帧。 |
| NumFrames |要处理的帧数 |
| Speed |用于加速输入视频的倍数。 |

下面是采用 JSON 和 XML 格式且符合要求的配置文件示例：

**XML 预设：**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**JSON 预设：**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a>包含 AMS .NET SDK 的 Microsoft Hyperlapse
以下方法将媒体文件上传为资产，然后使用 Azure Media Hyperlapse 媒体处理器来创建作业。

> [!NOTE]
> 为了使代码正常工作，应该事先在名为“context”的作用域中创建 CloudMediaContext。  若要了解详细信息，请阅读[内容管理文章](media-services-dotnet-get-started.md)。
> 
> [!NOTE]
> 字符串参数“hyperConfig”应是上述采用 JSON 或 XML 格式且符合要求的配置预设。
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>支持的文件类型
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相关链接
[Azure 媒体服务分析概述](media-services-analytics-overview.md)

[Azure 媒体分析演示](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

