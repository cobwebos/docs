---
title: "短信、电子邮件和 Webhook 的速率限制 | Microsoft 文档"
description: "了解 Azure 如何限制操作组中可能的短信、电子邮件或 webhook 通知数。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>短信、电子邮件和 webhook 发布的速率限制
速率限制是在发送给特定电话号码或电子邮件地址的通知太多时发生的通知挂起。 通过速率限制，确保警报处于管理且可操作状态。

用于短信和电子邮件的规则是相同的。 速率限制阈值为：

 - 短信：每小时 10 个消息。
 - 电子邮件：每小时 100 个消息。

## <a name="rate-limit-rules"></a>速率限制规则
- 特定电话号码或电子邮件在收到超过阈值所允许消息数时会进行速率限制。
- 电话号码或电子邮件可以是跨多个订阅的操作组的一部分。 速率限制应用于所有订阅。 一旦达到阈值便会应用，即使从多个订阅发送消息也是如此。  
- 当电话号码或电子邮件进行速率限制时，会发送一个附加通知以传达速率限制。 通知会在速率限制到期时进行声明。

## <a name="rate-limit-of-webhooks"></a>webhook 的速率限制 ##
未针对 webhook 实施速率限制。

## <a name="next-steps"></a>后续步骤 ##
* 详细了解[短信警报行为](monitoring-sms-alert-behavior.md)。
* 获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](monitoring-activity-log-alerts-on-service-notifications.md)。
