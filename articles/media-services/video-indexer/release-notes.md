---
title: Azure 媒体服务视频索引器发行说明 |Microsoft Docs
description: 为了及时了解最新的开发, 本文提供了 Azure 媒体服务视频索引器的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.openlocfilehash: a7d178972a8c8b413e3c6336c403348f1eb78abb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232596"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒体服务视频索引器发行说明

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英国南部中部署的视频索引器

你现在可以在英国南部区域创建视频索引器付费帐户。

### <a name="new-editorial-shot-type-insights-available"></a>新的编辑快照类型见解可用

添加到视频截图的新标记提供了编辑 "拍摄类型", 以使用内容创建工作流中所用的常见编辑短语来识别它们: 极端特写、特写、宽、中、两个拍摄、户外、室内、左面和右面 (可在JSON)。

### <a name="new-people-and-locations-entities-extraction-available"></a>新人员和位置实体提取可用

视频索引器通过视频的 OCR 和脚本来识别命名位置和用户通过自然语言处理 (NLP)。 视频索引器使用机器学习算法来识别在视频中调用特定位置 (例如, Eiffel 塔) 或人员 (例如 John Doe) 的时间。

### <a name="keyframes-extraction-in-native-resolution"></a>以本机分辨率提取关键帧

视频索引器提取的关键帧在视频的原始分辨率中可用。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>为从图像定型自定义面部模型提供 GA

从预览模式转为 GA 的图像 (可通过 API 和门户中获取) 进行的训练。

> [!NOTE]
> 与 "公开预览版" 过渡无关。

### <a name="hide-gallery-toggle-option"></a>隐藏库切换选项

用户可以选择在门户中隐藏 "库" 选项卡 (类似于隐藏 "示例" 选项卡)。
 
### <a name="maximum-url-size-increased"></a>最大 URL 大小增加

支持在为视频编制索引时使用 URL 查询字符串 4096 (而不是 2048)。
 
### <a name="support-for-multi-lingual-projects"></a>支持多语言项目

现在可以基于用不同语言编制索引的视频 (仅 API) 创建项目。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>作为小组件的编辑器

视频索引器 AI 编辑器现在作为要嵌入到客户应用程序的小组件提供。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>从门户更新隐藏式字幕文件中的自定义语言模型

在门户的 "自定义" 页中, 客户可以提供 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

## <a name="june-2019"></a>2019 年 7 月

### <a name="video-indexer-deployed-to-japan-east"></a>部署到日本东部的视频索引器

你现在可以在日本东部区域中创建视频索引器付费帐户。

### <a name="create-and-repair-account-api-preview"></a>创建和修复帐户 API (预览)

添加了新的 API, 可用于[更新 Azure 媒体服务连接终结点或密钥](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改进上传时的错误处理 

当基础 Azure 媒体服务帐户配置错误时, 将返回描述性消息。

### <a name="player-timeline-keyframes-preview"></a>播放机时间线关键帧预览 

你现在可以在播放机的时间线上查看每个时间的图像预览。

### <a name="editor-semi-select"></a>编辑器半选

你现在可以查看在编辑器中选择特定的见解时间范围时选择的所有见解的预览。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>从隐藏式字幕文件更新自定义语言模型

[创建自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)和[更新自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)API 现在支持将 VTT、SRT 和 TTML 文件格式作为语言模型的输入。

调用[更新视频脚本 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)时, 会自动添加脚本。 与视频相关的训练模型也会自动更新。 有关如何自定义和训练语言模型的信息, 请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新下载脚本格式– TXT 和 CSV

除了已支持隐藏式字幕格式 (SRT、VTT 和 TTML), 视频索引器现在支持下载 TXT 和 CSV 格式的脚本。

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
