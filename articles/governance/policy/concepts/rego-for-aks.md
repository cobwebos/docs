---
title: 了解如何审核虚拟机的内容
description: 了解 Azure 策略如何使用 Rego 和打开策略代理管理 Azure Kubernetes 服务上的群集。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453900"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>了解 Azure 策略的 Azure Kubernetes 服务

Azure 策略与集成[Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)(AKS) 群集上应用大规模强制要求和安全措施，以集中式、 一致的方式。
通过扩展的用法[网关守卫](https://github.com/open-policy-agent/gatekeeper)、_许可控制器 webhook_有关[打开策略代理](https://www.openpolicyagent.org/)(OPA)，Azure 策略使得管理和报告的相容性Azure 资源和 AKS 群集中的从一个位置的状态。

> [!NOTE]
> 适用于 AKS 的 azure 策略处于受限预览状态，只支持内置策略定义。

## <a name="overview"></a>概述

若要启用和使用 Azure 策略为 AKS 使用 AKS 群集，请执行以下操作：

- [参加预览功能](#opt-in-for-preview)
- [安装 Azure 策略外接程序](#installation-steps)
- [适用于 AKS 分配策略定义](#built-in-policies)
- [等待验证](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>参加预览版

安装 Azure 策略外接程序或之前启用的任何服务功能，你的订阅必须启用**Microsoft.ContainerService**资源提供程序和**Microsoft.PolicyInsights**资源提供程序，然后批准加入预览。 若要加入预览版，请执行以下步骤在 Azure 门户或使用 Azure CLI:

- Azure 门户：

  1. 注册**Microsoft.ContainerService**并**Microsoft.PolicyInsights**资源提供程序。 有关步骤，请参阅[资源提供程序和类型](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)。

  1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。  

     ![在所有服务中搜索策略](../media/rego-for-aks/search-policy.png)

  1. 选择**加入预览版**左侧和右侧的 Azure 策略页上。

     ![加入 AKS 预览版的策略](../media/rego-for-aks/join-aks-preview.png)

  1. 选择您要添加到预览版的订阅的行。

  1. 选择**自愿加入**的订阅列表顶部的按钮。

- Azure CLI：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Azure 策略外接程序

_Azure 策略外接程序_有关 Kubernetes 的 Azure 策略服务连接到网关守卫许可控制器。 外接程序，安装到_azure 策略_命名空间中，会制定的以下函数：

- 检查与 Azure 策略分配到 AKS 群集
- 下载并缓存策略的详细信息，包括_rego_策略定义，作为**configmaps**
- 运行完全扫描符合性检查，在 AKS 群集
- 审核报告和符合性详细信息备份到 Azure 策略

### <a name="installing-the-add-on"></a>安装该外接程序

#### <a name="prerequisites"></a>必备组件

在 AKS 群集中安装该外接程序之前，必须安装预览扩展。 使用 Azure CLI 完成此步骤：

1. 你需要 Azure CLI 版本 2.0.62 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. AKS 群集必须是版本_1.10_或更高版本。 使用以下脚本来验证你的 AKS 群集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安装版本_0.4.0_的 Azure cli 适用于 AKS，预览扩展`aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果您先前安装了_aks 预览版_扩展，安装任何更新使用`az extension update --name aks-preview`命令。

#### <a name="installation-steps"></a>安装步骤

完成先决条件后，你想要管理在 AKS 群集中安装的 Azure 策略外接程序。

- Azure 门户

  1. 通过单击启动在 Azure 门户中的 AKS 服务**所有服务**，然后搜索并选择**Kubernetes 服务**。

  1. 选择其中一个 AKS 群集。

  1. 选择**策略 （预览）** 左侧和右侧的 Kubernetes 服务页上。

     ![AKS 群集中的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主页上，选择**启用外接程序**按钮。

     ![启用 AKS 外接程序的 Azure 策略](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 如果**启用外接程序**按钮为灰显，订阅具有尚未添加到预览。 请参阅[预览版的自愿加入](#opt-in-for-preview)为所需的步骤。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>验证和报告频率

外接程序检查使用 Azure 策略的更改策略分配每隔 5 分钟。 在此刷新周期中，该外接程序中移除所有_configmaps_中_azure 策略_命名空间，然后重新创建_configmaps_守护程序使用。

> [!NOTE]
> 虽然_群集管理员_可能有权_azure 策略_命名空间，它已不推荐或支持对命名空间进行更改。 刷新周期内所做的任何手动更改将丢失。

每隔 5 分钟，该外接程序调用群集的完全扫描。 收集后完全扫描和任何实时评估的详细信息的网关守卫的尝试的更改到群集外, 接程序报告的结果返回到 Azure 策略以包含在[符合性详细信息](../how-to/get-compliance-data.md)喜欢的任何 Azure 策略分配。 在审核周期返回只用于活动策略分配的结果。

## <a name="policy-language"></a>策略语言

管理 AKS 的 Azure 策略语言结构遵循的现有策略。 效果_EnforceRegoPolicy_用于管理 AKS 群集，并采用_详细信息_属性特定于使用 OPA 和网关守卫。 有关详细信息和示例，请参阅[EnforceRegoPolicy](effects.md#enforceregopolicy)效果。

作为的一部分_details.policy_在策略定义中，Azure 策略的属性将 rego 策略的 URI 传递给该外接程序。 Rego 是 OPA 和网关守卫支持，以验证或转变到 Kubernetes 群集的请求的语言。 通过 Kubernetes 管理支持现有的标准，Azure 策略使用可以重复使用现有的规则并对它们与 Azure 策略的报表体验统一的云符合性。 有关详细信息，请参阅[什么是 Rego？](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)。

## <a name="built-in-policies"></a>内置策略

若要查找管理 AKS 使用 Azure 门户的内置策略，请执行以下步骤：

1. 在 Azure 门户中启动 Azure 策略服务。 选择**所有服务**中的左窗格中，然后搜索并选择**策略**。

1. 在 Azure 策略页的左窗格中，选择**定义**。

1. 从类别下拉列表框中，使用**全**若要清除筛选器，然后选择**Kubernetes 服务**。

1. 选择策略定义，然后选择**分配**按钮。

> [!NOTE]
> 分配 AKS 定义的 Azure 策略时**作用域**必须包含 AKS 群集资源。

或者，使用[门户分配策略-](../assign-policy-portal.md)快速入门来查找和分配 AKS 策略。 搜索 Kubernetes 策略定义，而不是示例审核 vm。

## <a name="logging"></a>日志记录

### <a name="azure-policy-add-on-logs"></a>Azure 策略外接程序日志

为 Kubernetes 控制器/容器，Azure 策略外接程序将日志保留在 AKS 群集。 日志中公开**Insights** AKS 群集的页。 有关详细信息，请参阅[了解 AKS 群集与 Azure Monitor 的容器的性能](../../../azure-monitor/insights/container-insights-analyze.md)。

### <a name="gatekeeper-logs"></a>网关守卫日志

若要启用的新资源请求的网关守卫日志，请按照中的步骤[启用和查看在 AKS 中日志的主节点的 Kubernetes](../../../aks/view-master-logs.md)。
下面是要查看已拒绝的活动上新的资源请求的示例查询：

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

若要查看的网关守卫容器日志，请按照中的步骤[启用和查看在 AKS 中日志的主节点的 Kubernetes](../../../aks/view-master-logs.md) ，并检查_kube apiserver_选项**诊断设置**窗格。

## <a name="remove-the-add-on"></a>删除外接程序

若要从 AKS 群集中删除的 Azure 策略外接程序，使用 Azure 门户或 Azure CLI:

- Azure 门户

  1. 通过单击启动在 Azure 门户中的 AKS 服务**所有服务**，然后搜索并选择**Kubernetes 服务**。

  1. 选择你想要禁用的 Azure 策略外接程序的 AKS 群集。

  1. 选择**策略 （预览）** 左侧和右侧的 Kubernetes 服务页上。

     ![AKS 群集中的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主页上，选择**禁用外接程序**按钮。

     ![禁用 AKS 外接程序的 Azure 策略](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>后续步骤

- 查看示例[Azure 策略示例](../samples/index.md)。
- 查看[策略定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/getting-compliance-data.md)。
- 了解如何[修正的不合规资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/index.md)，了解什么是管理组。