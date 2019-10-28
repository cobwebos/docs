---
title: Azure 媒体服务旧组件 |Microsoft Docs
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
ms.date: 10/27/2019
ms.author: juliako
ms.openlocfilehash: 30e8fa51df42d202d77ecdbc6a31fe3c7aaac6f4
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968620"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒体服务旧组件

随着时间的推移，对媒体服务组件进行了稳定的改进和增强。 因此，某些旧组件已经停用。 可以在以下文章中找到有关如何将应用程序从旧组件迁移到当前组件的说明。

## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>旧组件和迁移指南的停用计划

我们宣布弃用*Windows Azure 媒体编码器*（WAME）和*Azure 媒体编码器*（AME）媒体处理器。 这些处理器将于2019年11月30日停用。

* [从 Windows Azure 媒体编码器迁移到 Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [从 Azure 媒体编码器迁移到 Media Encoder Standard](migrate-azure-media-encoder.md)

我们还宣布停用以下媒体分析媒体处理器： 

|媒体处理器名称|停用日期|附加说明|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)| 2020年1月1日|此媒体处理器将替换为[Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)。 有关详细信息，请参阅[从 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2020年10月1日|此媒体处理器将替换为[Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)。 有关详细信息，请参阅[从 Azure Media Indexer 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)
|[Azure Media Face Detector](media-services-face-and-emotion-detection.md)|2020年2月1日|此媒体分析预览处理器将会停用，并且不会被移到 "公开上市"。 我们将针对未来投资评估其方案和用例。|
|[Azure Media Motion Detector](media-services-motion-detection.md)|2020年2月1日|此媒体分析预览处理器将会停用，并且不会被移到 "公开上市"。 我们将针对未来投资评估其方案和用例。|
|[Azure 媒体 OCR](media-services-video-optical-character-recognition.md)|2020年2月1日|此媒体处理器将由[Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)和[azure 媒体服务 V3 API 视频分析器预设](../latest/analyzing-video-audio-files-concept.md)替换。|
|[Azure Media Video Thumbnails](media-services-video-summarization.md)|2020年2月1日|此媒体处理器将由[Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)和[azure 媒体服务 V3 API 视频分析器预设](../latest/analyzing-video-audio-files-concept.md)替换。|

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-from-v2-to-v3.md)
