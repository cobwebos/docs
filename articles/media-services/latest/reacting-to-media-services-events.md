---
title: 响应 Azure 媒体服务事件 | Microsoft Docs
description: 使用 Azure 事件网格订阅媒体服务事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/19/2018
ms.author: juliako
ms.openlocfilehash: 143fec2ddb168b0fff0e419fa5767e9718637241
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465531"
---
# <a name="reacting-to-media-services-events"></a>响应媒体服务事件

媒体服务事件允许应用程序使用新式无服务器体系结构对不同事件（例如，作业状态更改事件）进行响应。 为此，它无需复杂的代码或高价低效的轮询服务。 相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向事件处理程序（如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)），甚至是向自己的 Webhook 推送事件，且仅需为已使用的内容付费。 有关定价的详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

媒体服务事件的可用性与事件网格[可用性](../../event-grid/overview.md)相关联，当事件网格在其他地区可用时，媒体服务事件也同样可用。  

## <a name="available-media-services-events"></a>可用的媒体服务事件

事件网格使用[事件订阅](../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。  目前，媒体服务事件订阅可包括以下事件：  

|事件名称|Description|
|----------|-----------|
| Microsoft.Media.JobStateChange| 作业状态更改时引发。 |
| Microsoft.Media.LiveEventConnectionRejected | 编码器的连接尝试被拒绝。 |
| Microsoft.Media.LiveEventEncoderConnected | 编码器与实时事件建立连接。 |
| Microsoft.Media.LiveEventEncoderDisconnected | 编码器断开连接。 |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | 媒体服务器删除了数据区块，因为该区块的抵达时间过迟，或者带有重叠的时间戳（新数据区块的时间戳小于前一数据区块的结束时间）。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | 媒体服务器收到流或连接中每个轨迹的第一个数据区块。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | 媒体服务器检测到音频和视频流不同步。用作警告，因为用户体验可能不受影响。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 媒体服务器检测到来自外部编码器的任意两个视频流不同步。用作警告，因为用户体验可能不受影响。 |
| Microsoft.Media.LiveEventIngestHeartbeat | 当实时事件正在运行时，每隔 20 秒为每个轨迹发布。 提供引入运行状况摘要。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | 媒体服务器检测到传入轨迹中存在不连续的情况。 |

## <a name="event-schema"></a>事件架构

媒体服务事件包含响应数据中的更改所需的所有信息。  可以识别媒体服务事件，因为 eventType 属性以“Microsoft.Media”开头。

有关详细信息，请参阅[媒体服务事件架构](media-services-event-schemas.md)。

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理媒体服务事件的应用程序应遵循以下建议的做法：

* 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的存储帐户。
* 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
* 忽略不了解的字段。  此做法有助于适应将来可能添加的新功能。
* 使用“subject”前缀和后缀匹配项，将事件限制为特定事件。

## <a name="next-steps"></a>后续步骤

[获取作业状态事件](job-state-events-cli-how-to.md)
