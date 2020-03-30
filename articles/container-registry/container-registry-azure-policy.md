---
title: 使用 Azure 策略的合规性
description: 在 Azure 策略中分配内置策略以审核 Azure 容器注册表的合规性
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330730"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>使用 Azure 策略审核 Azure 容器注册表的合规性

[Azure 策略](../governance/policy/overview.md)是 Azure 中用于创建、分配和管理策略的服务。 这些策略将在整个资源中强制实施不同的规则和效果，以便这些资源符合公司标准和服务级别协议。

本文介绍了 Azure 容器注册表的内置策略（预览）。 使用这些策略审核新的和现有的注册表是否符合规定。

使用 Azure 策略不收取任何费用。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="built-in-policy-definitions"></a>内置策略定义

以下内置策略定义特定于 Azure 容器注册表：

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

另请参阅内置网络策略定义[：[预览] 容器注册表应使用虚拟网络服务终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)。

## <a name="assign-policies"></a>分配策略

* 使用 Azure[门户](../governance/policy/assign-policy-portal.md)[、Azure CLI、](../governance/policy/assign-policy-azurecli.md)[资源管理器模板](../governance/policy/assign-policy-template.md)或 Azure 策略 SDK 分配策略。
* 将策略分配范围限定为资源组、订阅或 Azure[管理组](../governance/management-groups/overview.md)。 容器注册表策略分配适用于范围内的现有和新的容器注册表。
* 随时启用或禁用[策略实施](../governance/policy/concepts/assignment-structure.md#enforcement-mode)。

> [!NOTE]
> 分配或更新策略后，分配需要一些时间才能应用于定义范围内的资源。 有关[策略评估触发器的信息](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)。

## <a name="review-policy-compliance"></a>查看策略合规性

使用 Azure 门户、Azure 命令行工具或 Azure 策略 SDK 访问策略分配生成的合规性信息。 有关详细信息，请参阅[获取 Azure 资源的合规性数据](../governance/policy/how-to/get-compliance-data.md)。

有多种可能的原因会导致资源不合规。 要确定原因或找到对更改负责，请参阅[确定不合规](../governance/policy/how-to/determine-non-compliance.md)。

### <a name="policy-compliance-in-the-portal"></a>门户中的策略合规性：

1. 选择**所有服务**，然后搜索**策略**。
1. 选择**符合性**。
1. 使用筛选器限制合规性状态或在门户![](./media/container-registry-azure-policy/azure-policy-compliance.png)中搜索策略策略合规性。
1. 选择策略以查看聚合合规性详细信息和事件。 如果需要，请选择资源合规性的特定注册表。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI 中的策略合规性

您还可以使用 Azure CLI 获取合规性数据。 例如，使用 CLI 中的[az 策略分配列表](/cli/azure/policy/assignment#az-policy-assignment-list)命令获取所应用的 Azure 容器注册表策略的策略 ID：

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

示例输出：

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

然后运行[az 策略状态列表](/cli/azure/policy/state#az-policy-state-list)，以返回特定策略 ID 下所有资源的 JSON 格式的符合状态：

```azurecli
az policy state list \
  --resource <policyID>
```

或运行[az 策略状态列表](/cli/azure/policy/state#az-policy-state-list)以返回特定注册表资源的 JSON 格式的合规性状态，例如*我的注册表*：

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

* 了解有关 Azure 策略[定义](../governance/policy/concepts/definition-structure.md)和[效果](../governance/policy/concepts/effects.md)的更多

* 创建[自定义策略定义](../governance/policy/tutorials/create-custom-policy-definition.md)

* 了解有关 Azure[中治理功能](../governance/index.yml)的信息


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/