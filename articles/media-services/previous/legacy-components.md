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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: d4f4cfc005b2d5a63512245baee5230e9a26cb37
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309205"
---
# <a name="azure-media-services-legacy-components"></a>Azure 媒体服务旧组件

随着时间的推移，对媒体服务组件进行了稳定的改进和增强。 因此，某些旧组件已经停用。 可以在以下文章中找到有关如何将应用程序从旧组件迁移到当前组件的说明。

## <a name="legacy-components-and-migration-guidance"></a>旧组件和迁移指南

我们宣布弃用 Windows Azure 媒体编码器 (WAME) 和 Azure 媒体编码器 (AME) 媒体处理器。 这些处理器将于 2019 年 11 月 30 日停用。

* [从 Windows Azure 媒体编码器迁移到 Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [从 Azure 媒体编码器迁移到 Media Encoder Standard](migrate-azure-media-encoder.md)

我们还宣布弃用*Azure Media Indexer*和*Azure Media Indexer 2*。 [Azure Media Indexer](media-services-index-content.md)媒体处理器将在2020年10月1日停用。 [[Azure Media Indexer 2 （预览版）](media-services-process-content-with-indexer2.md)媒体处理器将在2019年1月1日停用。  [Azure 媒体服务视频索引器](https://docs.microsoft.com/azure/media-services/video-indexer/)替代了这些旧媒体处理器。

* [从 Azure Media Indexer 迁移并将 Azure Media Indexer 2 迁移到 Azure 媒体服务视频索引器](migrate-indexer-v1-v2.md)。

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](../latest/migrate-from-v2-to-v3.md)
