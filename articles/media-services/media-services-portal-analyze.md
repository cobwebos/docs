---
title: "使用 Azure 门户分析媒体 | Microsoft Docs"
description: "本主题讨论如何通过 Azure 门户使用媒体分析媒体处理器 (MP) 处理媒体。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 22032aef0cc8b7b015503043028873e70c21ee85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-your-media-using-the-azure-portal"></a>使用 Azure 门户分析媒体
> [!NOTE]
> 要完成本教程，需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

## <a name="overview"></a>概述
Azure 媒体服务分析是一系列语音和影像组件（具企业规模、合规性、安全性和全球性覆盖），让组织和企业可以从其视频文件中更轻松地获得可操作的见解。 有关 Azure 媒体服务分析的详细概述，请参阅[此](media-services-analytics-overview.md)主题。 

本主题讨论如何通过 Azure 门户使用媒体分析媒体处理器 (MP) 处理媒体。 媒体分析 MP 会生成 MP4 文件或 JSON 文件。 如果媒体处理器生成了 MP4 文件，可以采用渐进方式下载该文件。 如果媒体处理器生成了 JSON 文件，可以从 Azure Blob 存储下载该文件。 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>选择要分析的资产
1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“资产”。  
   。
    ![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. 选择要分析的资产，按“分析”按钮。
   
    ![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. 在“使用媒体分析处理媒体资产”窗口中，选择处理器。 
   
    本文的其余部分介绍每种处理器的功能和使用方式。 
5. 按“创建”，开始作业。

## <a name="azure-media-indexer"></a>Azure Media Indexer
通过 **Azure Media Indexer** 媒体处理器，可使媒体文件和内容具有可搜索性并可生成隐藏式字幕跟踪。 本部分提供可为此 MP 指定的选项的详细信息。

![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>语言
要在多媒体文件中识别的自然语言。 例如，英语或西班牙语。 

### <a name="captions"></a>字幕
可以选择要从内容生成的字幕格式。 索引作业可以生成以下格式的隐藏字幕文件：  

* **SAMI**
* **TTML**
* **WebVTT**

采用这些格式的隐藏字幕 (CC) 文件可用于使有听力障碍的用户能够访问音频和视频文件。

### <a name="aib-file"></a>AIB 文件
如果要生成与自定义的 SQL Server IFilter 搭配使用的音频索引 Blob 文件，请选择此选项。 有关详细信息，请参阅[此](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/)博客。

### <a name="keywords"></a>关键字
若要生成关键字 XML 文件，请选择此选项。 此文件包含从语音内容中提取的关键字以及频率和偏移量信息。

### <a name="job-name"></a>作业名称
可以识别出该作业的友好名称。 [此](media-services-portal-check-job-progress.md)文章介绍了如何监视作业的进度。 

### <a name="output-file"></a>输出文件
可以识别出输出内容的友好名称。 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse 是可以使用第一人称视角或运动相机内容创建流畅缩时视频的 MP。  有关详细信息，请参阅[此](media-services-hyperlapse-content.md)主题。 本部分提供可为此 MP 指定的选项的详细信息。

![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Speed
指定用于加速输入视频的速度。 输出是输入视频经过稳定和缩时转译的结果。

### <a name="job-name"></a>作业名称
可以识别出该作业的友好名称。 [此](media-services-portal-check-job-progress.md)文章介绍了如何监视作业的进度。 

### <a name="output-file"></a>输出文件
可以识别出输出内容的友好名称。 

## <a name="azure-media-face-detector"></a>Azure 媒体面部检测器
借助 **Azure Media Face Detector** 媒体处理器 (MP)，可通过面部表情来统计、跟踪动作，甚至计量受众的参与和反应。 此服务包含两项功能： 

* **人脸检测**
  
    人脸检测能够找出并跟踪视频中的人脸。 可以同时跟踪多个面部，随着对象移动持续进行跟踪，并将时间和位置的元数据以 JSON 文件的格式返回。 跟踪期间，该服务会在人员于屏幕上四处移动时，尝试为他们的面部赋予相同的 ID，即使他们被挡住或暂时离帧。
  
  > [!NOTE]
  > 此服务并不执行面部识别。 面部离帧或被挡住太久的人员，会在回来时赋予新的 ID。
  > 
  > 
* **情绪检测**
  
    情绪检测是面部检测媒体处理器的可选组件，它根据检测到的面部返回多个情绪属性的分析，包括快乐、悲伤、恐惧、愤怒等等。 

![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>检测模式
处理器可以使用以下模式之一：

* 人脸检测
* 根据人脸表情检测
* 聚合情感检测

### <a name="job-name"></a>作业名称
可以识别出该作业的友好名称。 [此](media-services-portal-check-job-progress.md)文章介绍了如何监视作业的进度。 

### <a name="output-file"></a>输出文件
可以识别出输出内容的友好名称。 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
借助 **Azure Media Motion Detector** 媒体处理器 (MP)，用户可在冗长且平淡的视频中有效识别出感兴趣的部分。 可以对静态相机数据片段使用动作检测，以识别视频中有动作的部分。 它会生成 JSON 文件，其中包含带时间戳的元数据，以及发生事件的边界区域。

此技术面向安全视频提要，它可以将动作分类为相关事件和误报（例如阴影或光源变化）。 这样，便可以在无需查看无止境的不相关事件的情况下，从相机输出生成安全警报，并从长时间的监控视频中提取感兴趣的片段。

![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure 媒体视频缩略图
此处理器可通过自动选择来自源视频的有趣片段帮助用户创建长视频的摘要。 要提供有关长视频内容的快速概述时，这很有用。 有关详细信息和示例，请参阅[使用 Azure Media Video Thumbnails 创建视频摘要](media-services-video-summarization.md)

![分析视频](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>作业名称
可以识别出该作业的友好名称。 [此](media-services-portal-check-job-progress.md)文章介绍了如何监视作业的进度。 

### <a name="output-file"></a>输出文件
可以识别出输出内容的友好名称。 

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

