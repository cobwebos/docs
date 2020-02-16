---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: aa9320ab4a2ff28c185ecef0f525376ceab4d875
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170195"
---
|名称 |说明 |作用 |版本 |
|---|---|---|---|
|[应从事件中心命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |服务中心客户端不应使用提供对命名空间中所有队列和主题的访问的命名空间级访问策略。 为了与最低权限安全模型保持一致，应在实体级别为队列和主题创建访问策略，以便仅提供对特定实体的访问权限 |Audit, Deny, 已禁用 |1.0.1 |
|[应针对事件中心实例定义授权规则](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |审核是否存在针对事件中心实体的授权规则，以便授予最低权限访问权限 |AuditIfNotExists, 已禁用 |1.0.0 |
|[应启用事件中心的诊断日志](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists, 已禁用 |2.0.0 |
