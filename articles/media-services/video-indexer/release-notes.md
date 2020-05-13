---
title: Azure 媒体服务视频索引器发行说明 |Microsoft Docs
description: 为了及时了解最新的开发，本文提供了 Azure 媒体服务视频索引器的最新更新。
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
ms.openlocfilehash: d78390aac51ea6fa70e1285b15dcc7ade74434ee
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124578"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒体服务视频索引器发行说明

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新小组件参数功能

**Insights**小组件包含新参数： `language` 和 `control` 。

**播放机**小组件具有新 `locale` 参数。 `locale`和 `language` 参数都控制播放机的语言。

有关详细信息，请参阅[小组件类型](video-indexer-embed-widgets.md#widget-types)部分。 

### <a name="new-player-skin"></a>新播放器外观

使用更新的设计启动了一个新的播放机外观。

### <a name="prepare-for-upcoming-changes"></a>准备即将进行的更改

* 目前，以下 Api 返回 account 对象：

    * [创建-付费](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [获取帐户](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [获取帐户-授权](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [带令牌的获取帐户](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    帐户对象的 `Url` 字段指向[视频索引器网站](https://www.videoindexer.ai/)的位置。
对于付费帐户，该 `Url` 字段当前指向内部 URL，而不是公共网站。
在接下来的几周，我们将更改它并返回所有帐户的[视频索引器网站](https://www.videoindexer.ai/)URL （试用版和付费版）。

    不要使用内部 Url，你应该使用[视频索引器公共 api](https://api-portal.videoindexer.ai/)。
* 如果要在应用程序中嵌入视频索引器 Url 并且 Url 未指向[视频索引器网站](https://www.videoindexer.ai/)或视频索引器 API 终结点（），而不是指向 `https://api.videoindexer.ai` 区域终结点（例如 `https://wus2.videoindexer.ai` ），请重新生成 url。

   为此，可以执行以下任一操作：

    * 将 URL 替换为指向视频索引器小组件 Api 的 URL （例如， [insights 小组件](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget)）
    * 使用视频索引器网站生成新的嵌入 URL：
         
         按 "**播放**" 转到视频页面-> 单击 " ** &lt; / &gt; 嵌入**" 按钮-> 将 URL 复制到应用程序中：
   
    区域 Url 不受支持，将在未来几周内被阻止。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>针对其他语言的自定义语言支持

视频索引器现在支持、和的自定义语言模型 `ar-SY` `en-UK` （仅适用于 `en-AU` API）。
 
### <a name="delete-account-timeframe-action-update"></a>删除帐户时限操作更新

删除帐户操作现在将在90天内而不是48小时内删除帐户。
 
### <a name="new-video-indexer-github-repository"></a>新视频索引器 GitHub 存储库

新的视频索引器 GitHub 包含不同的项目，现在可以使用入门指南和代码示例：https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger 更新

视频索引器将统一**身份验证**和**操作**合并成单个[视频索引器 OpenAPI 规范（swagger）](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)。 开发人员可以在[视频索引器开发人员门户](https://api-portal.videoindexer.ai/)中找到 api。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>用新 API 更新脚本

使用[更新-视频索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)API 更新脚本中的特定部分。

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>从视频索引器门户修复帐户配置

你现在可以更新媒体服务连接配置，以便自助解决以下问题： 

* Azure 媒体服务资源不正确
* 密码更改
* 媒体服务资源已在订阅之间移动  

若要修复帐户配置，请在视频索引器门户中导航到 "设置" > 帐户 "选项卡（作为所有者）。

### <a name="configure-the-custom-vision-account"></a>配置自定义远景帐户

使用视频索引器门户在付费帐户上配置自定义视觉帐户（以前，仅 API 支持此帐户）。 为此，请登录到视频索引器门户，选择 "模型自定义" > > 配置的动画字符。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>场景、截图和关键帧–现在，在一个见解窗格中

场景、截图和关键帧现在会合并为一个见解，以方便消耗和导航。 选择所需的场景时，可以看到它包含的快照和关键帧。 

### <a name="notification-about-a-long-video-name"></a>有关长视频名称的通知

当视频名称长度超过80个字符时，视频索引器将显示上传的描述性错误。

### <a name="streaming-endpoint-is-disabled-notification"></a>流式处理终结点已禁用通知

禁用流式处理终结点时，视频索引器将在播放机页上显示描述性错误。

### <a name="error-handling-improvement"></a>错误处理改进

现在，状态代码409将从[重新索引视频](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?)并[更新视频索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?)api 中返回，以防活动处于活动状态，以防止意外重写当前的重新索引更改。

## <a name="november-2019"></a>2019 年 11 月
 
* 朝鲜语自定义语言模型支持

    视频索引器现在支持 API 和门户中的朝鲜语（）的自定义语言模型 `ko-KR` 。 
* 支持语音到文本的新语言（STT）

    视频索引器 Api 现在支持阿拉伯语 Levantine （ar-SY）、英语英国方言（en-GB）和英语澳大利亚方言（zh-cn）中的 STT。
    
    对于视频上传，我们已将 zh-chs-HANS 替换为 zh-chs，两者均受支持，但建议使用 zh-chs-CN，更准确。
    
## <a name="october-2019"></a>2019 年 10 月
 
* 搜索库中的动画字符

    为经过动画处理的字符编制索引时，现在可以在帐户的视频条带中搜索它们。 有关详细信息，请参阅[动画字符识别](animated-characters-recognition.md)。

## <a name="september-2019"></a>2019 年 9 月
 
IBC 2019 上公布了多项改进：
 
* 动画字符识别（公共预览版）

    通过与自定义视觉集成，检测组广告是否能够识别动画内容中的字符。 有关详细信息，请参阅[动画字符检测](animated-characters-recognition.md)。
* 多语言标识（公共预览版）

    在音频轨中检测多种语言的段，并基于它们创建多语言脚本。 初始支持：英语、西班牙语、德语和法语。 有关详细信息，请参阅[自动识别和转录多语言内容](multi-language-identification-transcription.md)。
* 人员和位置的命名实体提取

    通过自然语言处理（NLP）从语音和视觉文本中提取品牌、位置和人物。
* 编辑快照类型分类

    用编辑类型（如近向、中等拍摄、两个拍摄、室内、户外等）标记照片。有关详细信息，请参阅[编辑快照类型检测](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主题推断增强功能-现在涵盖级别2
    
    主题推断 model 现在支持 IPTC 分类的深度粒度。 阅读有关 Azure 媒体服务的完整详细信息[新的 AI 支持的创新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英国南部中部署的视频索引器

你现在可以在英国南部区域创建视频索引器付费帐户。

### <a name="new-editorial-shot-type-insights-available"></a>新的编辑快照类型见解可用

添加到视频截图的新标记提供了编辑 "拍摄类型"，以使用内容创建工作流中所用的常见编辑短语来识别它们：极端特写、特写、宽、中、两个拍摄、户外、室内、左面和右面（适用于 JSON）。

### <a name="new-people-and-locations-entities-extraction-available"></a>新人员和位置实体提取可用

视频索引器通过视频的 OCR 和脚本来识别命名位置和用户通过自然语言处理（NLP）。 视频索引器使用机器学习算法来识别在视频中调用特定位置（例如，Eiffel 塔）或人员（例如 John Doe）的时间。

### <a name="keyframes-extraction-in-native-resolution"></a>以本机分辨率提取关键帧

视频索引器提取的关键帧在视频的原始分辨率中可用。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>为从图像定型自定义面部模型提供 GA

从预览模式转为 GA 的图像（可通过 API 和门户中获取）进行的训练。

> [!NOTE]
> 与 "公开预览版" 过渡无关。

### <a name="hide-gallery-toggle-option"></a>隐藏库切换选项

用户可以选择在门户中隐藏 "库" 选项卡（类似于隐藏 "示例" 选项卡）。
 
### <a name="maximum-url-size-increased"></a>最大 URL 大小增加

支持在为视频编制索引时使用 URL 查询字符串4096（而不是2048）。
 
### <a name="support-for-multi-lingual-projects"></a>支持多语言项目

现在可以基于用不同语言编制索引的视频（仅 API）创建项目。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>作为小组件的编辑器

视频索引器 AI 编辑器现在作为要嵌入到客户应用程序的小组件提供。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>从门户更新隐藏式字幕文件中的自定义语言模型

在门户的 "自定义" 页中，客户可以提供 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>部署到日本东部的视频索引器

你现在可以在日本东部区域中创建视频索引器付费帐户。

### <a name="create-and-repair-account-api-preview"></a>创建和修复帐户 API （预览）

添加了新的 API，可用于[更新 Azure 媒体服务连接终结点或密钥](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改进上传时的错误处理 

当基础 Azure 媒体服务帐户配置错误时，将返回描述性消息。

### <a name="player-timeline-keyframes-preview"></a>播放机时间线关键帧预览 

你现在可以在播放机的时间线上查看每个时间的图像预览。

### <a name="editor-semi-select"></a>编辑器半选

你现在可以查看在编辑器中选择特定的见解时间范围时选择的所有见解的预览。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>从隐藏式字幕文件更新自定义语言模型

[创建自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)和[更新自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)API 现在支持将 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

调用[更新视频脚本 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)时，会自动添加脚本。 与视频相关的训练模型也会自动更新。 有关如何自定义和训练语言模型的信息，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新下载脚本格式– TXT 和 CSV

除了已支持隐藏式字幕格式（SRT、VTT 和 TTML），视频索引器现在支持下载 TXT 和 CSV 格式的脚本。

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
