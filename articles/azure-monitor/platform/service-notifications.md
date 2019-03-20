---
title: 什么是 Azure 服务运行状况通知？
description: 借助服务运行状况通知，可以查看由 Microsoft Azure 发布的服务运行状况消息。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ab4aa2f37d5a883c83b8ee09b5de6551ebf13d2e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995155"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 门户查看服务运行状况通知

服务运行状况通知由 Azure 发布，包含有关订阅下资源的信息。 这些通知是活动日志事件的一个子类别，也可在活动日志中找到。 服务运行状况通知可能仅提供信息，也可能提示执行某个操作，具体取决于类别。

服务运行状况通知有多类：  

- **所需操作：** 当你的帐户发生异常情况时，Azure 可能会发出通知，并配合你解决相关问题。 Azure 会向用户发送通知，其中或详细列出需要执行的操作，或详细说明如何联系 Azure 工程或支持人员。  
- **辅助恢复：** 事件已发生，且工程师确认用户仍受事件影响。 Azure 工程人员需与用户协同解决问题，让服务完全恢复正常。  
- **事件：** 某个影响服务的事件当前正在影响订阅中的一个或多个资源。  
- **维护：** 某个可能会影响订阅中的一个或多个资源的计划内维护活动。  
- **信息：** 可能实现的优化，可帮助改善资源使用情况。 
- **安全性：** 与 Azure 上运行的解决方案相关的紧急安全性相关信息。

每个服务运行状况通知将包含有关适用范围和资源所受影响的详细信息。 详细信息包括：

属性名称 | 描述
-------- | -----------
channels | 以下值之一：**Admin** 或 **Operation**。
correlationId | 通常为字符串格式的 GUID。 属于同一操作的事件通常共享相同的 correlationId。
eventDataId | 事件的唯一标识符。
eventName | 事件的标题。
级别 | 事件的级别
resourceProviderName | 受影响资源的资源提供程序的名称。
resourceType| 受影响资源的资源类型。
subStatus | 通常为响应 REST 调用的 HTTP 状态码，但还可以包含其他用于描述子状态的字符串。 例如：正常(HTTP 状态代码:200)、已创建(HTTP 状态代码:201)、已接受(HTTP 状态代码:202)、没有任何内容(HTTP 状态代码:204)、错误的请求(HTTP 状态代码:400)、找不到(HTTP 状态代码:404)、冲突(HTTP 状态代码:409)、内部服务器错误(HTTP 状态代码:500)”、“服务不可用(HTTP 状态代码: 503)”和“网关超时(HTTP 状态代码: 504)。
eventTimestamp | 处理与事件对应的请求的 Azure 服务生成事件时的时间戳。
submissionTimestamp | 事件可供查询的时间戳。
subscriptionId | 记录此事件的 Azure 订阅。
status | 描述操作状态的字符串。 一些常见值如下：**Started**、**In Progress**、**Succeeded**、**Failed**、**Active** 和 **Resolved**。
operationName | 操作的名称。
category | 此属性始终为 ServiceHealth。
resourceId | 受影响的资源的资源 ID。
Properties.title | 此通信的本地化标题。 默认为英语。
Properties.communication | 带 HTML 标记的通信的经过本地化的详细信息。 默认为英语。
Properties.incidentType | 以下值之一：ActionRequired、Informational、Incident、Maintenance 或 Security。
Properties.trackingId | 与此事件关联的事件。 使用此属性将与某一事件相关的事件关联起来。
Properties.impactedServices | 转义 JSON blob，描述受事件影响的服务和区域。 属性包括服务列表，每个服务具有一个 ServiceName，以及一个 受影响区域的列表，其中每个区域具有一个 RegionName。
Properties.defaultLanguageTitle | 英语通信。
Properties.defaultLanguageContent | HTML 标记或纯文本格式的英语通信。
Properties.stage | **Incident** 和 **Security** 的可能值为 **Active**、**Resolved** 或 **RCA**。 对于 ActionRequired 或 Informational，唯一的值为 Active。 对于 **Maintenance**，可能的值为：**Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved** 或 **Complete**。
Properties.communicationId | 与此事件关联的通信。

### <a name="details-on-service-health-level-information"></a>服务运行状况级别信息的详细信息

**需要采取操作** (properties.incidentType == ActionRequired)
- 信息 - 需要管理员采取操作来防止影响现有服务
    
**维护** (properties.incidentType == Maintenance)
- 警告 - 紧急维护
- 信息 - 标准计划内维护

**信息** (properties.incidentType == Information)
- 信息 - 可能需要管理员采取操作来防止影响现有服务

**安全性** (properties.incidentType == Security)
- 错误 - 访问多个区域中多项服务时普遍出现的问题影响大批客户。
- 警告 - 访问特定服务和/或特定区域时出现的问题影响一部分客户。
- 信息 - 出现了影响管理操作和/或延迟，但不影响服务可用性的问题。

**服务问题** (properties.incidentType == Incident)
- 错误 - 访问多个区域中多项服务时普遍出现的问题影响大批客户。
- 警告 - 访问特定服务和/或特定区域时出现的问题影响一部分客户。
- 信息 - 出现了影响管理操作和/或延迟，但不影响服务可用性的问题。


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 门户中查看服务运行状况通知
1.  在 [Azure 门户](https://portal.azure.com)中，选择“监视”。

    ![Azure 门户菜单的屏幕快照，其中“监视器”处于选中状态](./media/service-notifications/home-monitor.png)

    Azure Monitor 将所有监视设置和数据汇聚到一个合并视图中。 首次打开的是“活动日志”部分。

3.  选择“**警报**”。

    ![监视活动日志的屏幕快照，其中“警报”处于选中状态](./media/service-notifications/service-health-summary.png)
4. 选择“+添加活动日志警报”并设置警报，以确保以后可收到服务通知。 有关详细信息，请参阅[创建有关服务通知的活动日志警报](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

## <a name="next-steps"></a>后续步骤
每当[发布服务运行状况通知时接收警报通知](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。  
详细了解[活动日志警报](../../azure-monitor/platform/activity-log-alerts.md)。

