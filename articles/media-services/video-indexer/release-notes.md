---
title: Azure 媒体服务视频索引器发布说明 |微软文档
description: 为了了解最新动态，本文为您提供了 Azure 媒体服务视频索引器的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: 5aa7abf7aafc14e71af5618cec892ef9f843d88a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733066"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒体服务视频索引器发布说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新的小部件参数功能

**见解**小部件包括新的参数：`language`和`control`。

**播放器**小部件有一个新的`locale`参数。 和`locale``language`参数都控制玩家的语言。

有关详细信息，请参阅[小部件类型](video-indexer-embed-widgets.md#widget-types)部分。 

## <a name="new-player-skin"></a>新玩家皮肤

一个新的玩家皮肤推出与更新的设计。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>对其他语言的自定义语言支持

视频索引器现在支持`ar-SY`的 自定义语言`en-UK`模型，`en-AU`和 （仅限 API）。
 
### <a name="delete-account-timeframe-action-update"></a>删除帐户时间范围操作更新

删除帐户操作现在在 90 天内删除帐户，而不是 48 小时。
 
### <a name="new-video-indexer-github-repository"></a>新的视频索引器 GitHub 存储库

新的视频索引器 GitHub 具有不同的项目、入门指南和代码示例，现已推出：https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>斯瓦格更新

视频索引器将**身份验证**和**操作**统一到单个[视频索引器 OpenAPI 规范（摇号）](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)中。 开发人员可以在[视频索引器开发人员门户](https://api-portal.videoindexer.ai/)中找到 API。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>使用新 API 更新脚本

使用[更新视频索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)API 更新脚本中的特定部分。

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>从视频索引器门户修复帐户配置

您现在可以更新媒体服务连接配置，以便自助解决诸如： 

* 不正确的 Azure 媒体服务资源
* 密码更改
* 媒体服务资源在订阅之间移动  

要修复帐户配置，在"视频索引器"门户中导航到"设置>帐户"选项卡（作为所有者）。

### <a name="configure-the-custom-vision-account"></a>配置自定义视觉帐户

使用视频索引器门户在付费帐户上配置自定义视觉帐户（以前，仅由 API 支持）。 为此，请登录到视频索引器门户，选择"模型自定义">动画角色>配置。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>场景、镜头和关键帧 – 现在在一个洞察窗格中

场景、镜头和关键帧现在合并为一个见解，以便更轻松地使用和导航。 当您选择所需的场景时，您可以看到它包含的镜头和关键帧。 

### <a name="notification-about-a-long-video-name"></a>有关长视频名称的通知

当视频名称超过 80 个字符时，视频索引器在上载时显示描述性错误。

### <a name="streaming-endpoint-is-disabled-notification"></a>流式处理终结点已禁用通知

禁用流式处理终结点后，视频索引器将在播放器页面上显示描述性错误。

### <a name="error-handling-improvement"></a>错误处理改进

状态代码 409 现在将从[重新索引视频和](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?)[更新视频索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?)API 返回，以防视频被主动编制索引，以防止意外覆盖当前的重新索引更改。

## <a name="november-2019"></a>2019 年 11 月
 
* 韩国自定义语言模型支持

    视频索引器现在在 API 和门户中支持`ko-KR`韩语 （ 的自定义语言模型. 
* 语音到文本 （STT） 支持的新语言

    视频索引器 API 现在支持阿拉伯语 Levantine （ar-SY）、英语英国方言 （en-GB） 和英语澳大利亚方言 （en-AU） 的 STT。
    
    对于视频上传，我们将 zh-HANS 替换为 zh-CN，两者都受支持，但建议 zh-CN 更准确。
    
## <a name="october-2019"></a>2019 年 10 月
 
* 在库中搜索动画角色

    索引动画角色时，现在可以在帐户的视频厨房中搜索它们。 有关详细信息，请参阅[动画字符识别](animated-characters-recognition.md)。

## <a name="september-2019"></a>2019 年 9 月
 
IBC 2019 年宣布的多项进展：
 
* 动画角色识别（公共预览）

    通过与自定义视觉的集成，能够检测群广告识别动画内容中的字符。 有关详细信息，请参阅[动画角色检测](animated-characters-recognition.md)。
* 多语言识别（公共预览）

    检测音轨中多种语言的段，并基于这些片段创建多语言脚本。 初步支持：英语、西班牙语、德语和法语。 有关详细信息，请参阅[自动识别和转录多语言内容](multi-language-identification-transcription.md)。
* 针对人员与位置的命名实体提取

    通过自然语言处理 （NLP） 从语音和视觉文本中提取品牌、位置和人员。
* 编辑拍摄类型分类

    标记具有编辑类型（如特写、中拍、两次拍摄、室内、室外等）的拍摄。有关详细信息，请参阅[编辑拍摄类型检测](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主题推断增强 - 现在涵盖级别 2
    
    主题推断模型现在支持 IPTC 分类的更深层粒度。 阅读 Azure[媒体服务新 AI 支持的创新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)的完整详细信息。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>部署在英国南部的视频索引器

您现在可以在英国南部区域创建视频索引器付费帐户。

### <a name="new-editorial-shot-type-insights-available"></a>提供新的编辑拍摄类型见解

添加到视频拍摄的新标签提供了编辑"拍摄类型"，用于识别内容创建工作流中使用的常见编辑短语，例如：极端特写、特写、宽、中、两次拍摄、室外、室内、左脸和右脸（在 JSON 中可用）。

### <a name="new-people-and-locations-entities-extraction-available"></a>提供新人员点和地点实体提取

视频索引器通过视频的 OCR 和转录通过自然语言处理 （NLP） 标识命名位置和人员。 视频索引器使用机器学习算法来识别特定位置（例如埃菲尔铁塔）或人员（例如，John Doe）在视频中被叫出来。

### <a name="keyframes-extraction-in-native-resolution"></a>原生分辨率的关键帧提取

视频索引器提取的关键帧以视频的原始分辨率提供。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>用于从图像中训练自定义人脸模型的 GA

从从预览模式移动到 GA 的图像（可通过 API 和门户提供）训练人脸。

> [!NOTE]
> 与"预览到 GA"过渡无关的定价影响。

### <a name="hide-gallery-toggle-option"></a>隐藏库切换选项

用户可以选择从门户隐藏库选项卡（类似于隐藏示例选项卡）。
 
### <a name="maximum-url-size-increased"></a>最大 URL 大小增加

支持对视频索引的 URL 查询字符串 4096（而不是 2048）。
 
### <a name="support-for-multi-lingual-projects"></a>支持多语言项目

现在可以基于以不同语言（仅限 API）索引的视频创建项目。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>编辑器作为小部件

视频索引器 AI 编辑器现在可作为小部件嵌入到客户应用程序中。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>从门户从隐藏字幕文件更新自定义语言模型

客户可以提供 VTT、SRT 和 TTML 文件格式作为门户自定义页中语言模型的输入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>部署到日本东部的视频索引器

您现在可以在日本东部区域创建视频索引器付费帐户。

### <a name="create-and-repair-account-api-preview"></a>创建和修复帐户 API（预览）

添加了一个新的 API，使您能够[更新 Azure 媒体服务连接终结点或密钥](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改进上传时的错误处理 

如果基础 Azure 媒体服务帐户配置错误，将返回描述性消息。

### <a name="player-timeline-keyframes-preview"></a>玩家时间线关键帧预览 

现在，您可以在播放器的时间线上看到每次的图像预览。

### <a name="editor-semi-select"></a>编辑器半选择

现在，您可以在编辑器中查看选择特定见解时间范围后选择的所有见解的预览。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>从隐藏字幕文件更新自定义语言模型

[创建自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)并[更新自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)API 现在支持 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

调用[更新视频脚本 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)时，将自动添加脚本。 与视频关联的训练模型也会自动更新。 有关如何自定义和训练语言模型的信息，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下载脚本格式 – TXT 和 CSV

除了已经支持的隐藏字幕格式（SRT、VTT 和 TTML），视频索引器现在支持以 TXT 和 CSV 格式下载脚本。

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
