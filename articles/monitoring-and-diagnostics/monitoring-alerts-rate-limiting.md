---
title: "短信、电子邮件和 Webhook 的速率限制 | Microsoft Docs"
description: "在活动日志中发生特定事件时，通过短信、webhook 和电子邮件接收通知。"
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
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: b2e954405500921c0c1e9c7cd71ce57130c98d64
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>短信、电子邮件和 Webhook 的速率限制
速率限制是在发送给特定电话号码或电子邮件的通知太多时发生的通知挂起。 速率限制可确保警报可管理且可操作

用于短信和电子邮件的规则是相同的。 速率限制阈值
 - 短信 - 每小时 10 个消息
 - 电子邮件 - 每小时 100 个消息

## <a name="rate-limit-rules"></a>速率限制规则
- 特定电话号码或电子邮件在收到消息超过速率时进行速率限制
- 电话号码或电子邮件可以是跨多个订阅的操作组的一部分。 速率限制应用于所有订阅，即，达到阈值后立即应用限制（即使是从多个订阅发送的）。  
- 当电话号码或电子邮件进行速率限制时，会发送一个附加通知以传达速率限制。 通知会在速率限制到期时进行声明。

## <a name="rate-limit-of-webhooks"></a>Webhook 的速率限制 ##
当前未针对 webhook 实施速率限制。

## <a name="next-steps"></a>后续步骤 ##
详细了解[短信警报行为](monitoring-sms-alert-behavior.md)  
获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报  
如何[配置每次发布服务运行状况通知时的警报](monitoring-activity-log-alerts-on-service-notifications.md)

