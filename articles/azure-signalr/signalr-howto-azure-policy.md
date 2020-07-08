---
title: 使用 Azure Policy 实现合规性
description: 将 Azure 策略中的内置策略分配给 Azure SignalR 服务资源的符合性。
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 1e0b6fbcacf13296d1d219da82d1b6f4c74ad7fb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85131953"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>使用 Azure 策略审核 Azure SignalR 服务资源的符合性

[Azure Policy](../governance/policy/overview.md) 是 Azure 中的一项服务，用于创建、分配和管理策略。 这些策略将在整个资源中强制实施不同的规则和效果，使这些资源符合公司标准和服务级别协议。

本文介绍 Azure SignalR 服务的内置策略（预览版）。 使用这些策略审核新的和现有的 SignalR 资源的符合性。

可以免费使用 Azure Policy。

## <a name="built-in-policy-definitions"></a>内置策略定义

以下内置策略定义特定于 Azure SignalR 服务：

[!INCLUDE [azure-policy-samples-policies-signalr](../../includes/policy/samples/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>分配策略定义

* 使用[Azure 门户](../governance/policy/assign-policy-portal.md)、 [Azure CLI](../governance/policy/assign-policy-azurecli.md)、[资源管理器模板](../governance/policy/assign-policy-template.md)或 Azure 策略 sdk 分配策略定义。
* 将策略分配的作用域限定为资源组、订阅或 [Azure 管理组](../governance/management-groups/overview.md)。 SignalR 策略分配适用于作用域内的现有和新 SignalR 资源。
* 可以随时启用或禁用[策略实施](../governance/policy/concepts/assignment-structure.md#enforcement-mode)功能。

> [!NOTE]
> 在分配或更新某个策略后，需要花费一些时间才会将分配应用到所定义作用域中的资源。 请查看有关[策略评估触发器](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)的信息。

## <a name="review-policy-compliance"></a>查看策略合规性

可以使用 Azure 门户、Azure 命令行工具或 Azure Policy SDK 来访问策略分配生成的合规性信息。 有关详细信息，请参阅[获取 Azure 资源的合规性数据](../governance/policy/how-to/get-compliance-data.md)。

有多种可能的原因会导致资源不合规。 若要确定原因或查找导致问题的变更，请参阅[确定不合规情况](../governance/policy/how-to/determine-non-compliance.md)。

### <a name="policy-compliance-in-the-portal"></a>门户中的策略合规性：

1. 选择“所有服务”  ，然后搜索“策略”。 
1. 选择“合规性”。 
1. 使用筛选器限制符合性状态或搜索策略
   
    [![门户 ](./media/signalr-howto-azure-policy/azure-policy-compliance.png) 中的策略符合性](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. 选择一个策略来查看聚合合规性详细信息和事件。 如果需要，请选择特定的 SignalR 以实现资源符合性。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI 中的策略合规性

也可使用 Azure CLI 来获取合规性数据。 例如，使用 CLI 中的[az policy 分配 list](/cli/azure/policy/assignment#az-policy-assignment-list)命令获取应用的 Azure SignalR 服务策略的策略 id：

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

示例输出：

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

然后，运行[az policy state list](/cli/azure/policy/state#az-policy-state-list)返回特定资源组下所有资源的 JSON 格式的符合性状态：

```azurecli
az policy state list --g <resourceGroup>
```

或运行[az policy state list](/cli/azure/policy/state#az-policy-state-list)返回特定 SignalR 资源的 JSON 格式符合性状态：

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>后续步骤

* 详细了解 Azure Policy [定义](../governance/policy/concepts/definition-structure.md)和[效果](../governance/policy/concepts/effects.md)

* 创建[自定义策略定义](../governance/policy/tutorials/create-custom-policy-definition.md)

* 详细了解 Azure 中的[治理功能](../governance/index.yml)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/