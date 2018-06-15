---
title: 什么是 Azure 服务运行状况通知？
description: 借助服务运行状况通知，可以查看由 Microsoft Azure 发布的服务运行状况消息。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2017
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: d6a87b17041c4ce6cf41da863354ef5a2a37141c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264436"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 门户查看服务运行状况通知

服务运行状况通知由 Azure 发布，包含有关订阅下资源的信息。 这些通知是活动日志事件的一个子类别，也可在活动日志中找到。 服务运行状况通知可能仅提供信息，也可能提示执行某个操作，具体取决于类别。

服务运行状况通知有多类：  

- **所需的操作：** 当发生了有关帐户的异常情况时，Azure 可能会发出通知，并协同你解决相关问题。 Azure 会向用户发送通知，其中或详细列出需要执行的操作，或详细说明如何联系 Azure 工程或支持人员。  
- **辅助恢复：** 事件已发生，且工程人员确认用户仍受事件影响。 Azure 工程人员需与用户协同解决问题，让服务完全恢复正常。  
- **事件：** 某个影响服务的事件当前正在影响订阅中的一个或多个资源。  
- **维护：** 某个计划的维护活动，可能会影响订阅中的一个或多个资源。  
- **信息：** 可能实现的优化，可帮助改进资源使用。 
- **安全性：** 与 Azure 上运行的解决方案相关的紧急安全性信息。

每个服务运行状况通知将包含有关适用范围和资源所受影响的详细信息。 详细信息包括：

属性名称 | 说明
-------- | -----------
channels | 以下值之一：“Admin”、“Operation”。
correlationId | 通常为字符串格式的 GUID。 属于同一操作的事件通常共享相同的 correlationId。
eventDataId | 事件的唯一标识符。
eventName | 事件的标题。
级别 | 事件的级别
resourceProviderName | 受影响资源的资源提供程序的名称。
resourceType| 受影响资源的资源类型。
subStatus | 通常为响应 REST 调用的 HTTP 状态码，但还可以包含其他用于描述子状态的字符串。 例如：OK（HTTP 状态代码：200）、Created（HTTP 状态代码：201）、Accepted（HTTP 状态代码：202）、No Content（HTTP 状态代码：204）、Bad Request（HTTP 状态代码：400）、Not Found（HTTP 状态代码：404）、Conflict（HTTP 状态代码：409）、Internal Server Error（HTTP 状态代码：500）、Service Unavailable（HTTP 状态代码：503）和 Gateway Timeout（HTTP 状态代码：504）。
eventTimestamp | 处理与事件对应的请求的 Azure 服务生成事件时的时间戳。
submissionTimestamp | 事件可供查询的时间戳。
subscriptionId | 记录此事件的 Azure 订阅。
status | 描述操作状态的字符串。 部分常用值包括：Started、In Progress、Succeeded、Failed、Active 和 Resolved。
operationName | 操作的名称。
category | 此属性始终为 ServiceHealth。
resourceId | 受影响的资源的资源 ID。
Properties.title | 此通信的本地化标题。 默认为英语。
Properties.communication | 带 HTML 标记的通信的经过本地化的详细信息。 默认为英语。
Properties.incidentType | 下列值之一：**ActionRequired**、**Information**、**Incident**、**Maintenance** 或 **Security**。
Properties.trackingId | 与此事件关联的事件。 使用此属性将与某一事件相关的事件关联起来。
Properties.impactedServices | 转义 JSON blob，描述受事件影响的服务和区域。 属性包括服务列表，每个服务具有一个 ServiceName，以及一个 受影响区域的列表，其中每个区域具有一个 RegionName。
Properties.defaultLanguageTitle | 英语通信。
Properties.defaultLanguageContent | HTML 标记或纯文本格式的英语通信。
Properties.stage | **Incident** 和 **Security** 的可能值为 **Active**、**Resolved** 或 **RCA**。 对于 **ActionRequired** 或 **Information**，唯一的值为 **Active**。 对于 **Maintenance**，可能值为：**Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved** 或 **Complete**。
Properties.communicationId | 与此事件关联的通信。

### <a name="details-on-service-health-level-information"></a>服务运行状况级别信息的详细信息
  <ul>
    <li><b>需要采取操作</b> (properties.incidentType == ActionRequired) <dl>
            <dt>信息</dt>
            <dd>需要管理员采取操作来防止影响现有服务</dd>
        </dl>
    </li>
    <li><b>维护</b> (properties.incidentType == Maintenance) <dl>
            <dt>警告</dt>
            <dd>紧急维护<dd>
            <dt>信息</dt>
            <dd>标准计划内维护</dd>
        </dl>
    </li>
    <li><b>信息</b> (properties.incidentType == Information) <dl>
            <dt>信息</dt>
            <dd>可能需要管理员采取操作来防止影响现有服务</dd>
        </dl>
    </li>
    <li><b>安全性</b> (properties.incidentType == Security) <dl>
            <dt>错误</dt>
            <dd>访问多个区域中的多项服务时普遍出现的问题影响大批客户。</dd>
            <dt>警告</dt>
            <dd>访问特定服务和/或特定区域时出现的问题影响一部分客户。</dd>
            <dt>信息</dt>
            <dd>出现了影响管理操作和/或延迟，但不影响服务可用性的问题。</dd>
        </dl>
    </li>
    <li><b>服务问题</b> (properties.incidentType == Incident) <dl>
            <dt>错误</dt>
            <dd>访问多个区域中的多项服务时普遍出现的问题影响大批客户。</dd>
            <dt>警告</dt>
            <dd>访问特定服务和/或特定区域时出现的问题影响一部分客户。</dd>
            <dt>信息</dt>
            <dd>出现了影响管理操作和/或延迟，但不影响服务可用性的问题。</dd>
        </dl>
    </li>
  </ul>

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 门户中查看服务运行状况通知
1.  在 [Azure 门户](https://portal.azure.com)中，选择“监视”。

    ![Azure 门户菜单的屏幕快照，其中“监视器”处于选中状态](./media/monitoring-service-notifications/home-monitor.png)

    Azure Monitor 将所有监视设置和数据汇聚到一个合并视图中。 首次打开的是“活动日志”部分。

3.  选择“**警报**”。

    ![监视活动日志的屏幕快照，其中“警报”处于选中状态](./media/monitoring-service-notifications/service-health-summary.png)
4. 选择“+添加活动日志警报”并设置警报，以确保以后可收到服务通知。 有关详细信息，请参阅[创建有关服务通知的活动日志警报](monitoring-activity-log-alerts-on-service-notifications.md)。

## <a name="next-steps"></a>后续步骤
每当[发布服务运行状况通知时接收警报通知](monitoring-activity-log-alerts-on-service-notifications.md)。  
详细了解[活动日志警报](monitoring-activity-log-alerts.md)。
