---
title: "什么是服务运行状况通知 | Microsoft Docs"
description: "借助服务运行状况通知，可以查看由 Microsoft Azure 发布的服务运行状况消息。"
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
ms.openlocfilehash: efdd42d244710b27fc33154b708cfbe40312e3b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="service-health-notifications"></a>服务运行状况通知
## <a name="overview"></a>概述

本文演示如何使用 Azure 门户查看服务运行状况通知。

借助服务运行状况通知，可以查看由 Azure 团队发布的服务运行状况消息，这些运行状况消息可能会影响订阅下的资源。 这些通知是活动日志事件的一个子类别，也可在活动日志中找到。 服务运行状况通知可能仅提供信息，也可能提示执行某个操作，具体取决于类别。

服务运行状况通知分为五类：  

- **需执行操作：**Azure 可能不时地发现帐户发生某种异常。 Azure 可能需要协同解决出现的问题。 Azure 将向用户发送通知，其中或详细列出需要执行的操作，或详细说明如何联系 Azure 工程或支持人员。  
- **辅助恢复：**事件已发生，且工程人员确认用户仍受事件影响。 Azure 工程人员需与用户协同解决问题，让服务完全恢复正常。  
- **事件：**某个影响服务的事件当前正在影响订阅中的一个或多个资源。  
- **维护：**此通知告知用户某个计划的维护活动可能会影响订阅中的一个或多个资源。  
- **信息：**Azure 不时地向用户发送通知，告知用户存在服务优化的可能性，可帮助提高资源利用率。  
- **安全性：**与 Azure 上运行的解决方案相关的紧急安全性信息。

每个服务运行状况通知将包含有关适用范围和资源所受影响的详细信息。 详细信息包括：

属性名称 | 说明
-------- | -----------
channels | 为以下值之一：“Admin”、“Operation”
correlationId | 通常为字符串格式的 GUID。 属于同一 uber 操作的事件通常共享相同的 correlationId。
eventDataId | 是事件的唯一标识符
eventName | 是事件的标题
级别 | 事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose”
resourceProviderName | 受影响资源的资源提供程序的名称
resourceType| 受影响资源的资源类型
subStatus | 通常为相应 REST 调用的 HTTP 状态代码，但也可能包括用于描述子状态的其他字符串，例如以下常用值：OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）、Gateway Timeout（HTTP 状态代码：504）。
eventTimestamp | 处理与事件对应的请求的 Azure 服务生成事件时的时间戳。
submissionTimestamp |   事件可供查询的时间戳。
subscriptionId | 其中记录了此事件的 Azure 订阅
status | 描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active、Resolved。
operationName | 操作的名称。
category | "ServiceHealth"
resourceId | 受影响资源的资源 ID。
Properties.title | 此通信的本地化标题。 默认语言为英语。
Properties.communication | 带 HTML 标记的通信的经过本地化的详细信息。 默认为英语。
Properties.incidentType | 可能的值：AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | 标识与此事件关联的事件。 使用此属性将与某一事件相关的事件关联起来。
Properties.impactedServices | 转义 JSON blob，描述受事件影响的服务和区域。 服务列表，每个服务具有 ServiceName 和一个 ImpactedRegions（其中每个具有一个 RegionName）列表。
Properties.defaultLanguageTitle | 英语通信
Properties.defaultLanguageContent | html 标记或纯文本格式的英语通信
Properties.stage | 对于 AssistedRecovery、ActionRequired、Information、Incident、Security，可能的值为：Active、Resolved。 对于 Maintenance，可能值为：Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | 与此事件关联的通信。


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 门户中查看服务运行状况通知
1.  在[门户](https://portal.azure.com)中，导航到“监视”服务

    ![监视](./media/monitoring-service-notifications/home-monitor.png)
2.  单击“监视”选项打开“监视”体验。 Azure Monitor 将所有监视设置和数据汇聚到一个合并视图中。 首次打开的是“活动日志”部分。

3.  现在单击“警报”部分

    ![监视](./media/monitoring-service-notifications/service-health-summary.png)
4. 单击“+添加活动日志警报”并配置警报，以确保以后可收到服务通知。 若要详细了解如何针对服务通知配置警报，请[访问“活动日志警报和服务通知”页](monitoring-activity-log-alerts-on-service-notifications.md)。

## <a name="next-steps"></a>后续步骤：
每当[发布服务运行状况通知时接收警报通知](monitoring-activity-log-alerts-on-service-notifications.md)  
深入了解[活动日志警报](monitoring-activity-log-alerts.md)
