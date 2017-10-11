---
title: "媒体服务平台上的媒体分析 | Microsoft Docs"
description: "媒体分析公共预览版概述，其中集合了企业级的语音和计算机视觉服务、合规性、安全性和全球市场宣传功能"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: c0bbe6f80370515fa783b12757434897fe2221b6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="media-analytics-on-the-media-services-platform"></a>媒体服务平台上的媒体分析
## <a name="overview"></a>概述
越来越多的组织将视频用作首选媒体来培训员工、与客户沟通以及规定业务职能。 云计算提供了一种方法来存储、流式传输和访问这些大型媒体文件。 但随着公司视频内容库的扩大，它需要在从内容中提取见解时具有同样有效的方式。 

为了满足这一不断增长的需求，Azure 媒体服务提供了 Azure 媒体分析。 媒体分析是语音和视觉组件的集合，便于组织和企业从视频文件中汲取可以实施的见解。 媒体分析通过使用核心媒体服务平台组件构建而成，一开始就能进行大规模的媒体处理。

借助媒体分析，开发人员可以快速将高级视频功能引入应用程序中。 它提供企业环境，可满足各种规模的大型组织所需的合规性、安全性和全球市场宣传需求。

下图显示媒体分析和媒体服务平台的其他主要部分。 

![VoD 工作流](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

媒体分析媒体处理器会生成 MP4 文件或 JSON 文件。 如果媒体处理器生成了 MP4 文件，可以采用渐进方式下载该文件。 如果媒体处理器生成了 JSON 文件，可以从 Azure Blob 存储下载该文件。 

## <a name="media-analytics-services"></a>媒体分析服务

### <a name="indexer"></a>索引器
借助 Azure Media Indexer 可使内容可搜索并生成隐藏式字幕跟踪。 与以前的版本相比，Azure Media Indexer 2 预览版编制索引的速度更快，并且语言支持更广泛。 支持的语言包括英语、西班牙语、法语、德语、意大利语、中文、葡萄牙语和阿拉伯语。 有关详细信息和示例，请参阅[使用 Azure Media Indexer 2 处理视频](media-services-process-content-with-indexer2.md)。
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse 结合了视频稳定化和慢镜头功能，可从长格式内容创建快速可用的视频。 除了创建慢镜头视频，还可以使用 Hyperlapse 将通过手机和摄影机拍摄的不稳定视频创建稳定的视频。 有关详细信息和示例，请参阅[使用 Azure Media Hyperlapse 创建 Hyperlapse 媒体文件](media-services-hyperlapse-content.md)。
### <a name="motion-detector"></a>动作检测器
可以使用移动探测器来检测背景平稳的视频中的动作。 这样，便可以检查监控摄像机所检测到的动作事件是否存在误报。 有关详细信息和示例，请参阅 [Azure 媒体分析的动作检测](media-services-motion-detection.md)。
### <a name="face-detector"></a>面部检测器
使用面部检测器可以检测人脸及情绪，包括快乐、悲伤和惊讶。 此服务具有多个有用行业应用程序（稍后将进行介绍），包括聚合与分析参与事件的人员的反应。 有关详细信息和示例，请参阅 [Azure 媒体分析的人脸和情绪检测](media-services-face-and-emotion-detection.md)。
### <a name="video-summarization"></a>视频摘要
视频摘要可通过自动选择来自源视频的有趣片段帮助你创建长视频的摘要。 如果想要提供有关长视频内容的快速概述，此功能会很有用。 有关详细信息和示例，请参阅[使用 Azure Media Video Thumbnails 创建视频摘要](media-services-video-summarization.md)。
### <a name="optical-character-recognition"></a>光学字符识别
借助 Azure 媒体 OCR（光学字符识别），可以将视频文件中的文本内容转换成可编辑、可搜索的数字文本。 可以从媒体的视频信号中自动提取有意义的元数据。
### <a name="scalable-face-redaction"></a>可缩放的面部修订
Azure 媒体修订器是一种媒体分析媒体处理器，可用于在云中进行可缩放的面部修订。 使用面部修订，可对视频进行修改，使所选个人的面部模糊显示。 用户可能想要在新闻媒体中或涉及公共安全时使用面部修订服务。 对于时长仅几分钟但包含多张面孔的镜头，进行手动面部修订可能需要几个小时，但使用此服务仅需几个简单步骤即可完成面部修订。 有关详细信息，请参阅[使用 Azure 媒体分析进行面部修订](media-services-face-redaction.md)文章。

## <a name="common-scenarios"></a>常见方案
媒体分析可帮助组织和企业从视频搜集新的见解，并更有效地管理大量视频内容。 下面介绍几种方案：

* **呼叫中心**。 即使出现了社交媒体，客户呼叫中心仍能帮助解决大量的客户服务事务。 此音频数据中编码了大量客户信息，分析这些信息可实现更高的客户满意度。 通过使用媒体索引器，组织可以提取文本并生成搜索索引和仪表板。 然后，他们可以围绕常见抱怨、投诉源和其他相关数据提取情报。
* **用户生成的内容仲裁**。 从新闻媒体分支机构到公安部门，许多组织都设立了对外公开的门户，用于在其中接受用户生成的媒体，例如视频和图像。 内容的数量可能因意外事件而激增。 在这些情况下，仅凭人力几乎不可能有效地审查内容的适宜性。 客户可以依赖于内容仲裁服务，将工作重点放在适当的内容上。
* **监控**。 由于 IP 相机使用量的增加，监控视频的数量呈爆炸式增长。 人工审查监控视频既耗时又容易发生人为错误。 媒体分析提供多项服务（例如动作检测、面部检测和 Hyperlapse），以简化审查、管理和创建衍生对象的过程。

## <a name="media-analytics-media-processors"></a>媒体分析媒体处理器
本节列出媒体分析媒体处理器，并说明如何使用 .NET 或 REST 来获取媒体处理器 (MP) 对象。

### <a name="mp-names"></a>MP 名称
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure 媒体面部检测器
* Azure Media Motion Detector
* Azure 媒体视频缩略图
* Azure 媒体 OCR

### <a name="net"></a>.NET
以下函数采用其中一个指定的 MP 名称，并返回 MP 对象。

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


### <a name="rest"></a>REST
请求：

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

响应：

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>演示
请参阅 [Azure 媒体分析演示](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相关文章
请参阅[媒体服务分析公告](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)。

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
