---
title: Azure 安全中心警报的架构
description: 本文介绍了 Azure 安全中心用于安全警报的不同架构。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 894fc4066ad408f0749e7a982011d82b205e6fec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90901341"
---
# <a name="security-alerts-schemas"></a>安全警报架构

如果你的订阅已启用 Azure Defender，则当安全中心检测到其资源的威胁时，你将收到安全警报。

可以在 Azure 安全中心的 **威胁防护** 页中查看这些安全警报，也可以通过以下外部工具查看：

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) -Microsoft 的云本地 SIEM。 Sentinel 连接器从 Azure 安全中心获取警报，并将其发送到 Azure Sentinel 的 [Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 。
- 第三方 Siem-使用安全中心的 [连续导出](continuous-export.md) 工具将数据发送到 [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)。 然后，将事件中心数据与第三方 SIEM 进行集成。
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) -如果你使用 REST API 访问警报，请参阅 [联机警报 API 文档](https://docs.microsoft.com/rest/api/securitycenter/alerts)。

如果使用任何编程方法来使用警报，将需要正确的架构来查找与您相关的字段。 此外，如果您要导出到事件中心或尝试使用泛型 HTTP 连接器来触发工作流自动化，请使用这些架构来正确分析 JSON 对象。

>[!IMPORTANT]
> 对于每种方案，架构略有不同，因此请确保选择下面的相关选项卡。


## <a name="the-schemas"></a>架构 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[工作流自动化和连续导出到事件中心](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>发送到逻辑应用、事件中心和第三方 Siem 的警报的示例 JSON

下面你会发现传递到的警报事件的架构：

- 在安全中心的工作流自动化中配置的 Azure 逻辑应用实例
- 使用安全中心的连续导出功能的 Azure 事件中心

有关工作流自动化功能的详细信息，请参阅 [自动响应警报和建议](workflow-automation.md)。
有关连续导出的详细信息，请参阅 [导出警报和建议](continuous-export.md)。

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel 和 Log Analytics 工作区](#tab/schema-sentinel)

Sentinel 连接器从 Azure 安全中心获取警报，并将其发送到 Azure Sentinel 的 Log Analytics 工作区。 

若要使用安全中心警报创建 Sentinel 事例或事件，你将需要如下所示的警报的架构。 

有关 Azure Sentinel 的详细信息，请参阅 [文档](https://docs.microsoft.com/azure/sentinel/)。

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure 活动日志](#tab/schema-activitylog)

Azure 安全中心审核在 Azure 活动日志中生成作为事件的安全警报。

可以通过按如下所示搜索 "激活警报" 事件来查看活动日志中的安全警报事件：

[![在活动日志中搜索激活警报事件](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>发送到 Azure 活动日志的警报的示例 JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>架构的数据模型

|字段|说明|
|----|----|
|**channels**|常量、"Operation"|
|**correlationId**|Azure 安全中心警报 ID|
|description|警报的描述|
|**eventDataId**|请参阅 correlationId|
|**名**|值和 localizedValue 子字段包含警报显示名称|
|**category**|值和 localizedValue 子字段是常量-"Security"|
|**eventTimestamp**|生成警报时的 UTC 时间戳|
|**id**|完全限定的警报 ID|
|**level**|常量、"信息性"|
|**operationId**|请参阅 correlationId|
|**operationName**|值字段是常量-"Microsoft. Security/位置/警报/激活/操作"，本地化值将是 "激活警报" (可能会将其本地化为与用户区域设置) |
|**resourceGroupName**|将包括资源组名称|
|**resourceProviderName**|值和 localizedValue 子字段是常量-"Microsoft. Security"|
|**resourceType**|值和 localizedValue 子字段是常量-"Microsoft. Security/位置/警报"|
|**resourceId**|完全限定的 Azure 资源 ID|
|**status**|值和 localizedValue 子字段固定为 "活动"|
|**subStatus**|值和 localizedValue 子字段为空|
|**submissionTimestamp**|事件提交到活动日志的 UTC 时间戳|
|**subscriptionId**|已泄露资源的订阅 ID|
|**properties**|与警报相关的其他属性的 JSON 包。 它们可能会从一条警报更改为另一种，但会在所有警报中显示以下字段：<br>-严重性：攻击的严重性<br>-compromisedEntity：已泄露资源的名称<br>-remediationSteps：要采取的补救步骤数组<br>-意向：警报的终止链意向。 可能的意向记录在[意向表](alerts-reference.md#intentions)中|
|**relatedEvents**|常量-空数组|
|||





### <a name="ms-graph-api"></a>[MS 图形 API](#tab/schema-graphapi)

Microsoft Graph 是 Microsoft 365 中的数据和智能的网关。 它提供了一个统一的可编程性模型，可用于访问 Microsoft 365、Windows 10 和企业移动性 + 安全性中的大量数据。 在 Microsoft Graph 中使用丰富的数据来构建与数百万用户交互的组织和使用者的应用程序。

[Microsoft Graph 文档](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)中提供了发送到 MS Graph 的安全警报的架构和 JSON 表示形式。

---


## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心的威胁防护工具在发送安全警报信息时使用的架构。

有关从外部安全中心访问安全警报的方法的详细信息，请参阅以下页面：

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) -Microsoft 的云-本机 SIEM
- [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/) -Microsoft 的完全托管的实时数据引入服务
- 安全中心的 [连续导出功能](continuous-export.md)
- [Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) -Azure Monitor 将日志数据存储在 Log Analytics 工作区中，包含数据和配置信息的容器
