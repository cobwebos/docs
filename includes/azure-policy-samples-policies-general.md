---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169904"
---
|名称 |说明 |作用 |版本 |
|---|---|---|---|
|[允许的位置](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |通过此策略，可限制组织在部署资源时可指定的位置。 用于强制执行异地符合性要求。 排除资源组、Microsoft.AzureActiveDirectory/b2cDirectories 以及使用“全局”区域的资源。 |deny |1.0.0 |
|[允许的资源组位置](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |通过此策略，可限制组织可以创建资源组的位置。 用于强制执行异地符合性要求。 |deny |1.0.0 |
|[允许的资源类型](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |此策略可用于指定组织可以部署的资源类型。 只有支持“tags”和“location”的资源类型才会受此策略影响。 若要限制所有资源，请复制此策略并将“mode”更改为“All”。 |deny |1.0.0 |
|[审核资源位置是否匹配资源组位置](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |审核资源位置是否与其资源组位置匹配。 |审核 |1.0.0 |
|[审核自定义 RBAC 规则的使用情况](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |审核“所有者、参与者、读者”等内置角色而不是容易出错的自定义 RBAC 角色。 使用自定义角色被视为例外，需要进行严格的审查和威胁建模 |Audit, 已禁用 |1.0.0 |
|[不应存在自定义订阅所有者角色](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |此策略确保不存在自定义订阅所有者角色。 |Audit, 已禁用 |1.0.0 |
|[不允许的资源类型](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |此策略可用于指定组织无法部署的资源类型。 |拒绝 |1.0.0 |
