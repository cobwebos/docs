---
title: Azure 媒体服务视频索引器发行说明 |Microsoft Docs
description: 若要保持最新状态的最新的开发成果，本文向您提供在 Azure 媒体服务视频索引器上最新的更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454017"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒体服务视频索引器发行说明

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能

## <a name="june-2019"></a>2019 年 7 月

### <a name="video-indexer-deployed-to-japan-east"></a>视频索引器部署到日本东部

现在可以创建付费帐户在日本东部地区视频索引器。

### <a name="create-and-repair-account-api-preview"></a>创建和修复帐户 API （预览）

添加一个新 API，使你能够[更新的 Azure 媒体服务连接终结点或密钥](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改进的错误处理上传 

发生基础 Azure 媒体服务帐户的配置不正确时返回的描述性消息。

### <a name="player-timeline-keyframes-preview"></a>播放机时间线的关键帧预览 

现在可以在播放机的时间线上看到每次图像预览。

### <a name="editor-semi-select"></a>编辑器以选择

现在可以看到结果在编辑器中选择的特定信息时间范围选择的所有见解的预览。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>更新自定义语言模型中隐藏式字幕文件

[创建自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)并[更新自定义语言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)Api 现在支持 VTT，SRT，和 TTML 文件格式作为输入的语言模型。

调用时[更新视频脚本 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)，脚本会自动添加。 与视频关联的培训模型也会自动更新。 有关如何自定义和训练语言模型的信息，请参阅[自定义具有视频索引器的语言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新下载脚本格式 – TXT 和 CSV

除了关闭隐藏字幕支持的格式已 （SRT、 VTT 和 TTML），视频索引器现在支持下载脚本 TXT 和 CSV 格式。

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
