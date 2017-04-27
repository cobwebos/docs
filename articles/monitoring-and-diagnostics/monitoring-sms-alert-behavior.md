---
title: "操作组中的短信通知行为 |Microsoft Docs"
description: "短信格式，以及回复短信以取消订阅、重新订阅或请求帮助。"
author: anirudhcavale
manager: carmonm
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
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 10f33898fb86bd2449994a153d99cb59dc6078d6
ms.lasthandoff: 03/31/2017


---
# <a name="sms-alert-behavior-in-action-groups"></a>操作组中的短信通知行为
## <a name="overview"></a>概述 ##
通过操作组可以配置接收方的列表。 定义活动日志警报时可以利用这些组；确保触发活动日志警报时向特定操作组发送通知。 所支持的一种警报机制是短信；警报支持双向通信。 用户可以响应警报以：

- **取消订阅警报：**用户可以为所有操作组或单个操作组取消订阅所有短信警报。  
- **重新订阅通知：**用户可以为所有操作组或单个操作组重新订阅所有短信警报。  
- **请求帮助：**用户可以询问有关短信的详细信息。 用户将被重定向到此文

本文介绍短信警报的行为，以及用户根据其区域设置可采取的响应操作：

## <a name="usacanada-sms-behavior"></a>美国/加拿大短信行为
### <a name="receiving-an-sms-alert"></a>接收短信警报
触发警报时，属于操作组的短信接收方将收到短信。 短信包含以下信息：
* 此警报发送到的操作组的短名称
- 警报的标题

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>为单个操作组取消订阅短信警报
用户可通过关键字：“DISABLE &lt;操作组的短名称&gt;”回应短代码 20873，为一个操作组取消订阅短信警报。

例如： 如果用户希望为短名称为“Azure”的操作组取消订阅警报，可向短代码 20873 发送短信“DISABLE Azure”

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>为所有操作组取消订阅短信警报
用户可通过以下任何关键字回应短代码 20873，为所有操作组取消订阅短信警报：
* STOP

例如： 如果用户希望为所有操作组取消订阅所有短信警报，可以向短代码 20873 发送短信“STOP”

>[!NOTE]
>如果用户已取消订阅短信警报，但随后被添加到新的操作组，将接收新操作组的短信警报，但对于所有以前的操作组均保持取消订阅状态。
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>为一个操作组重新订阅短信警报
用户可通过关键字：“ENABLE &lt;操作组的短名称&gt;”回应短代码 20873，为一个操作组重新订阅短信警报。

例如： 如果用户希望为短名称为“Azure”的操作组重新订阅警报，可向短代码 20873 发送短信“ENABLE Azure”

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>为所有操作组重新订阅短信警报
用户可通过以下任何关键字回应短代码 20873，为所有操作组重新订阅短信警报：

* START

例如： 如果用户希望为所有操作组重新订阅所有短信警报，可向短代码 20873 发送短信“START”

### <a name="requesting-help-via-sms"></a>通过短信请求帮助
用户可使用以下任何关键字回应短代码 20873，询问有关所收到短信的详细信息：
* HELP

将向用户发送带本文链接的回复信息。

## <a name="next-steps"></a>后续步骤
获取[活动日志警报概述](monitoring-overview-alerts.md)，了解如何接收警报  
了解有关[短信速率限制](monitoring-alerts-rate-limiting.md)的详细信息  
详细了解[操作组](monitoring-action-groups.md)

