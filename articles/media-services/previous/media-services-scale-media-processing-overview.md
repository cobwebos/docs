---
title: 媒体保留单位概述 |Microsoft Docs
description: 本文概述了如何使用 Azure 媒体服务调整媒体处理。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 8867e680be0aba187daf83bc538dd47c582c71fe
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618499"
---
# <a name="media-reserved-units"></a>媒体保留单位

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

使用 Azure 媒体服务，可以通过管理媒体保留单位 (Mru) 来缩放媒体处理。 MRU 提供编码媒体所需的额外计算能力。 Mru 数决定了处理媒体任务的速度，以及在帐户中可并发处理的媒体任务数。 例如，如果你的帐户有5个 Mru，并且有要处理的任务，则可以同时运行5个媒体任务。 所有剩余任务都将排队，并在正在运行的任务完成时，可以按顺序进行处理。 你预配的每个 MRU 都将导致容量保留，但不会为你提供专用资源。 在需求极高的时间，所有 Mru 可能都不会立即开始处理。

## <a name="choosing-between-different-reserved-unit-types"></a>在不同的预留单位类型之间进行选择

下表有助于在不同的编码速度之间进行选择时做出决定。  它显示了7分钟、1080p 视频的编码持续时间（取决于所使用的 MRU）。

|RU 类型|方案|7 分钟 1080p 视频的示例结果 |
|---|---|---|
| **S1**|单比特率编码。 <br/>具有 SD 或更低分辨率的文件，不具有高时效性，成本低。|使用“H264 单比特率 SD 16x9”编码为单比特率 SD 分辨率 MP4 文件大约需要 7 分钟。|
| **S2**|单比特率和多比特率编码。<br/>SD 和 HD 编码的正常使用情况。|预设为“H264 单比特率 720p”的编码大约需要 6 分钟。<br/><br/>预设为“H264 多比特率 720p”的编码大约需要 12 分钟。|
| **S3**|单比特率和多比特率编码。<br/>全高清和 4K 分辨率视频。 对时间敏感，更快的编码周转。|预设为“H264 单比特率 1080p”的编码大约需要 3 分钟。<br/><br/>预设为“H264 多比特率 1080p”的编码大约需要 8 分钟。|

> [!NOTE]
> 如果你没有为你的帐户设置 MRU，你的媒体任务将使用 S1 MRU 的性能进行处理，并且将按顺序选取任务。 不保留任何处理能力，因此，两个任务之间的等待时间将取决于系统中资源的可用性。

## <a name="considerations"></a>注意事项

* 对于媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议使用10个 S3 单位来设置帐户。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。
* 对于没有 Mru 的编码任务，你的任务在排队状态下花费的时间可能没有上限，并且一次只能运行一个任务。

## <a name="billing"></a>计费

根据在帐户中预配的媒体预留单位的分钟数计费。 这与帐户中是否有作业运行无关。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。

## <a name="quotas-and-limitations"></a>配额和限制

有关配额和限制以及如何开具支持票证的信息，请参阅[配额和限制](media-services-quotas-and-limitations.md)。

## <a name="next-steps"></a>后续步骤

尝试通过以下技术之一调整媒体处理能力：

[.Net](media-services-dotnet-encoding-units.md) 
[门户网站](media-services-portal-scale-media-processing.md) 
[REST](/rest/api/media/operations/encodingreservedunittype) 
[Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples) 
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)