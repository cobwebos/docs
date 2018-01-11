---
title: "短信、电子邮件、Azure 应用推送通知和 webhook 的速率限制 | Microsoft Docs"
description: "了解 Azure 如何限制操作组中可能的短信、电子邮件、Azure 应用推送通知或 webhook 通知数。"
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>短信、电子邮件、Azure 应用推送通知和 webhook 帖子的速率限制
速率限制是在发送给特定电话号码、电子邮件地址或设备的通知太多时发生的通知挂起。 通过速率限制，确保警报处于管理且可操作状态。

速率限制阈值为：

 - **SMS**：每 5 分钟最多 1 条短信。
 - 电子邮件：每小时 100 个消息。
 - **Azure 应用推送通知**：未针对推送通知设置速率限制。
 - **Webhook**：未针对 Webhook 设置速率限制。

## <a name="rate-limit-rules"></a>速率限制规则
- 特定电话号码或电子邮件在收到超过阈值所允许消息数时会进行速率限制。
- 电话号码或电子邮件可以是跨多个订阅的操作组的一部分。 速率限制应用于所有订阅。 一旦达到阈值便会应用，即使从多个订阅发送消息也是如此。  
- 当对电子邮件地址进行速率限制时，会发送一个附加通知以传达速率限制。 通知会在速率限制到期时进行声明。

## <a name="next-steps"></a>后续步骤 ##
* 详细了解[短信警报行为](monitoring-sms-alert-behavior.md)。
* 获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报。  
* 了解如何[配置每次发布服务运行状况通知时的警报](monitoring-activity-log-alerts-on-service-notifications.md)。
