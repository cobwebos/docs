---
title: Azure 媒体服务实时事件错误代码 |Microsoft Docs
description: 本文列出了实时事件错误代码。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599465"
---
# <a name="media-services-live-event-error-codes"></a>媒体服务实时事件错误代码

下表列出了[实时事件](live-events-outputs-concept.md)错误代码：

|错误|说明|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|当传入的编码器正在发送超过30fps 的流以编码实时事件/频道时，会发生此错误。|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|当传入的编码器正在发送超过以下分辨率的流时，将出现此错误：用于对实时事件/通道进行编码的1920x1088 和用于传递实时事件/通道的 4096 x 2160。|

## <a name="see-also"></a>另请参阅

[流式处理终结点（源）错误代码](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>后续步骤

[教程：通过 Media Services 实时流](stream-live-tutorial-with-api.md)
