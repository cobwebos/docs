---
title: 响应 Azure 媒体服务事件 | Microsoft Docs
description: 使用 Azure 事件网格订阅媒体服务事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782686"
---
# <a name="reacting-to-media-services-events"></a>响应媒体服务事件

媒体服务事件允许应用程序使用新式无服务器体系结构对不同事件（例如，作业状态更改事件）进行响应。 为此，它无需复杂的代码或高价低效的轮询服务。 相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向事件处理程序（如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)），甚至是向自己的 Webhook 推送事件，且仅需为已使用的内容付费。 有关定价的详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

媒体服务事件的可用性与事件网格[可用性](../../event-grid/overview.md)相关联，当事件网格在其他地区可用时，媒体服务事件也同样可用。  

## <a name="available-media-services-events"></a>可用的媒体服务事件

事件网格使用[事件订阅](../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。  当前，媒体服务事件订阅可包含以下事件类型：  

|事件名称|说明|
|----------|-----------|
| Microsoft.Media.JobStateChange| 作业状态更改时引发。 |

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
