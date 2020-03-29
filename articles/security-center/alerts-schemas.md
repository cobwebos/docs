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
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062949"
---
# <a name="security-alerts-schemas"></a>安全警报架构

当安全中心检测到对其资源的威胁时，Azure 安全中心的标准层的用户将收到安全警报。

您可以在 Azure 安全中心**的威胁保护**页面或通过外部工具（如：

- [Azure 哨兵](https://docs.microsoft.com/azure/sentinel/)- 微软的云原生 SIEM。 Sentinel 连接器从 Azure 安全中心获取警报，并将它们发送到 Azure Sentinel 的[日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。
- 第三方 SIEM - 使用安全中心的[连续导出](continuous-export.md)工具将数据发送到[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)。 然后，将事件中心数据与第三方 SIEM 集成。
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) - 如果您使用 REST API 访问警报，请参阅[联机警报 API 文档](https://docs.microsoft.com/rest/api/securitycenter/alerts)。

如果您使用任何编程方法使用警报，则需要正确的架构来查找与您相关的字段。 此外，如果要导出到事件中心或尝试使用通用 HTTP 连接器触发工作流自动化，请使用架构正确解析 JSON 对象。

>[!IMPORTANT]
> 对于每种方案，架构略有不同，因此请确保选择下面的相关选项卡。


## <a name="the-schemas"></a>架构 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[工作流自动化和连续导出到事件中心](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>针对发送到逻辑应用、事件中心和第三方 SIEM 的警报的 JSON 示例

下面您将找到传递给的警报事件的架构：

- 在安全中心的工作流自动化中配置的 Azure 逻辑应用实例
- 使用安全中心的连续导出功能的 Azure 事件中心

有关工作流自动化功能的详细信息，请参阅[自动响应警报和建议](workflow-automation.md)。
有关连续导出的详细信息，请参阅[导出警报和建议](continuous-export.md)。

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure 哨兵和日志分析工作区](#tab/schema-sentinel)

Sentinel 连接器从 Azure 安全中心获取警报，并将其发送到 Azure 哨兵的日志分析工作区。 

要使用安全中心警报创建 Sentinel 案例或事件，您需要下面显示的这些警报的架构。 

有关 Azure 哨兵的详细信息，请参阅[文档](https://docs.microsoft.com/azure/sentinel/)。

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure 活动日志](#tab/schema-activitylog)

Azure 安全中心审核生成安全警报作为 Azure 活动日志中的事件。

您可以通过搜索激活警报事件来查看活动日志中的安全警报事件，如下所示：

[![搜索活动日志以查找激活警报事件](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


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

|字段|描述|
|----|----|
|**channels**|常量，"操作"|
|**相关性Id**|Azure 安全中心警报 ID|
|**描述**|警报说明|
|**eventDataId**|请参阅关联 Id|
|**事件名称**|值和本地化价值子字段包含警报显示名称|
|**类别**|值和本地化价值子字段是常量 - "安全"|
|**eventTimestamp**|生成警报时的 UTC 时间戳|
|**id**|完全合格的警报 ID|
|**水平**|常量，"信息"|
|**操作 Id**|请参阅关联 Id|
|**操作名称**|值字段是常量 - "Microsoft.安全/位置/警报/激活/操作"，本地化值将为"激活警报"（可能是用户区域设置的本地化标准）|
|**资源组名称**|将包括资源组名称|
|**resourceProviderName**|值和本地化价值子字段是常数 - "Microsoft.Security"|
|**资源类型**|值和本地化价值子字段是常量 - "Microsoft.安全/位置/警报"|
|**资源 Id**|完全限定的 Azure 资源 ID|
|**状态**|值和本地化价值子字段是常量 - "活动"|
|**subStatus**|值和本地化价值子字段为空|
|**submissionTimestamp**|事件提交到活动日志的 UTC 时间戳|
|**订阅 Id**|已入侵资源的订阅 ID|
|**性能**|与警报相关的其他属性的 JSON 包。 这些字段可能会从一个警报更改为另一个警报，但是，以下字段将显示在所有警报中：<br>- 严重性：攻击的严重程度<br>- 泄露的实体：被入侵资源的名称<br>- 补救步骤：要采取的补救步骤数组<br>- 意图：警报的杀伤链意图。 可能的意图记录在["意图"表中](alerts-reference.md#intentions)|
|**relatedEvents**|常量 - 空数组|
|||





### <a name="ms-graph-api"></a>[MS 图形 API](#tab/schema-graphapi)

微软图形是微软365数据和智能的网关。 它提供了一个统一的可编程模型，可用于访问 Office 365、Windows 10 和企业移动性 + 安全性中的大量数据。 使用 Microsoft Graph 中丰富的数据为与数百万用户交互的组织和消费者构建应用。

发送到 MS Graph 的安全警报的架构和 JSON 表示形式在[Microsoft 图形文档中](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)可用。

---


## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心的威胁保护工具在发送安全警报信息时使用的架构。

有关从安全中心外部访问安全警报的方法的详细信息，请参阅以下页面：

- [Azure 哨兵](https://docs.microsoft.com/azure/sentinel/)- 微软的云原生 SIEM
- [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)- 微软完全托管的实时数据引入服务
- 安全中心的[连续导出功能](continuous-export.md)
- [日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)- Azure 监视器将日志数据存储在日志分析工作区中，该工作区包含数据和配置信息
