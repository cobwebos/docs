---
title: 什么是 Azure 服务运行状况通知？
description: 借助服务运行状况通知，可以查看由 Microsoft Azure 发布的服务运行状况消息。
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: e40538ac98bbc7b79311d4fb0da7568d56a84e18
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77653962"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 门户查看服务运行状况通知

服务运行状况通知由 Azure 发布，包含有关订阅下资源的信息。 这些通知是活动日志事件的一个子类别，也可在活动日志中找到。 服务运行状况通知可能仅提供信息，也可能提示执行某个操作，具体取决于类别。

服务运行状况通知有多类：  

- **所需的操作：** 当发生了有关帐户的异常情况时，Azure 可能会发出通知，并协同你解决相关问题。 Azure 会向用户发送通知，其中或详细列出需要执行的操作，或详细说明如何联系 Azure 工程或支持人员。  
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
Properties.incidentType | 以下值之一： " **ActionRequired**"、"**信息**"、"**事件**"、"**维护**" 或 "**安全**"。
Properties.trackingId | 与此事件关联的事件。 使用此属性将与某一事件相关的事件关联起来。
Properties.impactedServices | 转义 JSON blob，描述受事件影响的服务和区域。 属性包括服务列表，每个服务具有一个 ServiceName，以及一个 受影响区域的列表，其中每个区域具有一个 RegionName。
Properties.defaultLanguageTitle | 英语通信。
Properties.defaultLanguageContent | HTML 标记或纯文本格式的英语通信。
Properties.stage | **事件**和**安全**的可能值为 **"活动"、"** **已解决**" 或 " **RCA**"。 对于 ActionRequired 或 Informational，唯一的值为 Active。 对于 **Maintenance**，可能值为：**Active**、**Planned**、**InProgress**、**Canceled**、**Rescheduled**、**Resolved** 或 **Complete**。
Properties.communicationId | 与此事件关联的通信。

### <a name="details-on-service-health-level-information"></a>服务运行状况级别信息的详细信息

**需要采取操作** (properties.incidentType == ActionRequired)
- 信息-需要管理员操作以防止对现有服务的影响。
    
**维护** (properties.incidentType == Maintenance)
- 警告-紧急维护
- 信息性-标准计划内维护

**信息** (properties.incidentType == Information)
- 信息-管理员可能需要防止对现有服务的影响。

**安全性** (properties.incidentType == Security)
- 警告-影响现有服务并可能需要管理员操作的安全建议。
- 信息-影响现有服务的安全建议。

**服务问题** (properties.incidentType == Incident)
- 错误 - 访问多个区域中多项服务时普遍出现的问题影响大批客户。
- 警告 - 访问特定服务和/或特定区域时出现的问题影响一部分客户。
- 信息 - 出现了影响管理操作和/或延迟，但不影响服务可用性的问题。
