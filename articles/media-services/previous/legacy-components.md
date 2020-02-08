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
ms.date: 11/22/2019
ms.author: juliako
ms.openlocfilehash: cfc9d944f7851f9b802e18c861fd16438171b8e7
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069569"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒体服务旧组件

随着时间的推移，对媒体服务组件进行了稳定的改进和增强。 因此，某些旧组件已经停用。 可以在以下文章中找到有关如何将应用程序从旧组件迁移到当前组件的说明。
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>旧组件和迁移指南的停用计划

我们宣布弃用*Windows Azure 媒体编码器*（WAME）和*Azure 媒体编码器*（AME）媒体处理器。 这些处理器将于2020年3月31日停用。

* [从 Windows Azure 媒体编码器迁移到 Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [从 Azure 媒体编码器迁移到 Media Encoder Standard](migrate-azure-media-encoder.md)

我们还宣布停用以下媒体分析媒体处理器： 
 
|媒体处理器名称|停用日期|附加说明|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|2020年1月1日|[Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)正在替换此媒体处理器。 有关详细信息，请参阅[从 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)。|
|[Azure Media Indexer](media-services-index-content.md)|2023年3月1日|[Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)正在替换此媒体处理器。 有关详细信息，请参阅[从 Azure Media Indexer 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-from-v2-to-v3.md)
