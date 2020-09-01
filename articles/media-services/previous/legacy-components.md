---
title: Azure 媒体服务旧组件 | Microsoft Docs
description: 本主题介绍 Azure 媒体服务旧组件。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269516"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒体服务旧组件

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

随着时间的推移，对媒体服务组件进行了稳定的改进和增强。 因此，某些旧组件已经停用。 可以在以下文章中找到有关如何将应用程序从旧组件迁移到当前组件的说明。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>旧组件的停用计划和迁移指南

我们宣布弃用 Windows Azure 媒体编码器 (WAME) 和 Azure 媒体编码器 (AME) 媒体处理器。 这些处理器将于 2020 年 3 月 31 日停用。

* [从 Windows Azure 媒体编码器迁移到 Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [从 Azure 媒体编码器迁移到 Media Encoder Standard](migrate-azure-media-encoder.md)

我们还宣布停用以下媒体分析媒体处理器： 
 
|媒体处理器名称|停用日期|附加说明|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|2020年1月1日|[Azure 媒体服务视频索引器](../video-indexer/index.yml)正在替换此媒体处理器。 有关详细信息，请参阅 [从 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2023 年 3 月 1 日|[Azure 媒体服务视频索引器](../video-indexer/index.yml)正在替换此媒体处理器。 有关详细信息，请参阅 [从 Azure Media Indexer 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)|
|[动作检测](media-services-motion-detection.md)|2020 年 6 月 1 日|目前无替换计划。|
|[视频摘要](media-services-video-summarization.md)|2020 年 6 月 1 日|目前无替换计划。|
|[视频光学字符识别](media-services-video-optical-character-recognition.md)|2020 年 6 月 1 日|[Azure 媒体服务视频索引器](../video-indexer/index.yml)正在替换此媒体处理器。 另外，请考虑使用 [Azure 媒体服务 V3 API](../latest/analyzing-video-audio-files-concept.md)。 <br/>请参阅 [比较 Azure 媒体服务 v3 预置和视频索引器](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[面部检测器](media-services-face-and-emotion-detection.md)|2020 年 6 月 1 日|[Azure 媒体服务视频索引器](../video-indexer/index.yml)正在替换此媒体处理器。 另外，请考虑使用 [Azure 媒体服务 V3 API](../latest/analyzing-video-audio-files-concept.md)。 <br/>请参阅 [比较 Azure 媒体服务 v3 预置和视频索引器](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[内容审查器](media-services-content-moderation.md)|2020 年 6 月 1 日|[Azure 媒体服务视频索引器](../video-indexer/index.yml)正在替换此媒体处理器。 另外，请考虑使用 [Azure 媒体服务 V3 API](../latest/analyzing-video-audio-files-concept.md)。 <br/>请参阅 [比较 Azure 媒体服务 v3 预置和视频索引器](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-from-v2-to-v3.md)
