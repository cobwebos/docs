---
title: 了解 Azure 库伯内斯服务的 Azure 策略
description: 了解 Azure 策略如何使用 Rego 和打开策略代理来管理 Azure 库伯奈斯服务上的群集。
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372649"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>了解适用于 Azure Kubernetes 服务的 Azure Policy

Azure 策略与[Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)（AKS） 集成，以便以集中、一致的方式对群集应用大规模实施和安全措施。
通过扩展使用[Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3（[开放策略代理](https://www.openpolicyagent.org/)（OPA） 的_准入控制器 Webhook，Azure_策略可以从一个位置管理和报告 Azure 资源和 AKS 群集的合规性状态。

> [!IMPORTANT]
> AKS 的 Azure 策略处于预览状态，仅支持内置策略定义。 内置策略属于**库伯奈斯**类别。 **"执行再策略"** 效果和相关**库伯内斯服务**类别策略正在被_弃用_。 相反，请使用更新的[强制OPA约束](./effects.md#enforceopaconstraint)效果。

> [!WARNING]
> 此功能尚未在所有区域都可用。 有关推出状态，请参阅[AKS 问题 - 策略加载项的中断更改](https://github.com/Azure/AKS/issues/1529)。

## <a name="overview"></a>概述

要启用 AKS 的 Azure 策略并将其与 AKS 群集配合使用，请执行以下操作：

- [选择启用预览功能](#opt-in-for-preview)
- [安装 Azure 策略加载项](#installation-steps)
- [为 AKS 分配策略定义](#built-in-policies)
- [等待验证](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>选择加入预览

在安装 Azure 策略加载项或启用任何服务功能之前，订阅必须启用**Microsoft.ContainerService 资源**提供程序和**Microsoft.PolicyInsights**资源提供程序，然后获得批准才能加入预览。 要加入预览，请按照 Azure 门户或 Azure CLI 中的以下步骤操作：

- Azure 门户：

  1. 注册**Microsoft.ContainerService 服务和** **Microsoft.政策见解**资源提供商。 有关步骤，请参阅[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

  1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。********

     ![在所有服务中搜索策略](../media/rego-for-aks/search-policy.png)

  1. 选择 Azure 策略页面左侧的 **"加入预览**"。

     ![加入 AKS 预览策略](../media/rego-for-aks/join-aks-preview.png)

  1. 选择要添加到预览的订阅行。

  1. 选择订阅列表顶部的 **"选择加入**"按钮。

- Azure CLI：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Azure 策略加载项

库伯内斯的_Azure 策略加载项_将 Azure 策略服务连接到门卫准入控制器。 加载项安装在_kube 系统_命名空间中，将实现以下功能：

- 使用 Azure 策略服务检查群集的分配。
- 将群集中的策略部署为[约束模板](https://github.com/open-policy-agent/gatekeeper#constraint-templates)和[约束](https://github.com/open-policy-agent/gatekeeper#constraints)自定义资源。
- 将审核和合规性详细信息报告回 Azure 策略服务。

### <a name="installing-the-add-on"></a>安装加载项

#### <a name="prerequisites"></a>先决条件

在 AKS 群集中安装加载项之前，必须安装预览扩展。 此步骤使用 Azure CLI 完成：

1. 如果安装了 Gatekeeper v2 策略，请删除 **"策略（预览）"** 页上 AKS 群集上的 **"禁用**"按钮的加载项。

1. 您需要 Azure CLI 版本 2.0.62 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. AKS 群集必须是版本_1.14_或更高版本。 使用以下脚本验证 AKS 群集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安装 AKS 的 Azure CLI 预览扩展版_0.4.0，_ `aks-preview`：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果您以前安装了_aks 预览_扩展，请使用 命令`az extension update --name aks-preview`安装任何更新。

#### <a name="installation-steps"></a>安装步骤

完成先决条件后，在要管理的 AKS 群集中安装 Azure 策略加载项。

- Azure 门户

  1. 单击 **"所有服务**"，然后搜索并选择**库伯奈斯服务**，在 Azure 门户中启动 AKS 服务。

  1. 选择其中一个 AKS 群集。

  1. 选择库伯内斯服务页面左侧**的策略（预览）。**

     ![来自 AKS 群集的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主页中，选择"**启用加载项**"按钮。

     ![启用 AKS 加载项的 Azure 策略](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 如果**启用加载项**按钮已灰显，则订阅尚未添加到预览版。 有关所需步骤，请参阅[选择加入](#opt-in-for-preview)预览。 如果**禁用**按钮可用，则仍安装网守 v2，必须删除。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>验证和报告频率

加载项使用 Azure 策略服务签入每 15 分钟检查策略分配中的更改。
在此刷新周期中，加载项将检查更改。 这些更改触发器创建、更新或删除约束模板和约束。

> [!NOTE]
> 虽然群集管理员可能有权创建和更新约束模板和约束资源，但这些方案不受支持，因为手动更新将被覆盖。

每 15 分钟，加载项将调用群集的完整扫描。 在收集完全扫描的详细信息以及 Gatekeeper 对群集尝试的更改的任何实时评估后，加载项将结果报告回 Azure 策略服务，以便像任何 Azure 策略分配一样包含在[合规性详细信息](../how-to/get-compliance-data.md#portal)中。 在审核周期中仅返回活动策略分配的结果。 审核结果也可以被视为失败约束的状态字段中列出的[违规行为](https://github.com/open-policy-agent/gatekeeper#audit)。

## <a name="policy-language"></a>政策语言

用于管理库伯奈斯的 Azure 策略语言结构遵循现有策略。 效果_强制OPA约束_用于管理 Kubernetes 群集，并获取特定于使用[OPA 约束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和门卫 v3 的详细信息属性。 有关详细信息和示例，请参阅[强制OPA约束](./effects.md#enforceopaconstraint)效果。
  
作为策略定义中_的详细信息.约束模板_和_详细信息.约束_属性的一部分，Azure 策略将这些[自定义资源定义](https://github.com/open-policy-agent/gatekeeper#constraint-templates)（CRD） 的 URI 传递给加载项。 Rego 是 OPA 和门卫支持的语言，用于验证对 Kubernetes 群集的请求。 通过支持 Kubernetes 管理的现有标准，Azure 策略可以重用现有规则并将其与 Azure 策略配对，从而获得统一的云合规性报告体验。 有关详细信息，请参阅什么是[Rego？。](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="built-in-policies"></a>内置策略

要查找使用 Azure 门户管理群集的内置策略，请按照以下步骤操作：

1. 在 Azure 门户中启动 Azure 策略服务。 选择左侧窗格中的所有服务，然后搜索并选择 **"策略**"。

1. 在 Azure 策略页的左侧窗格中，选择 **"定义**"。

1. 在"类别"下拉列表框中，使用"全部选择"清除筛选器，然后选择**Kubernetes**。

1. 选择策略定义，然后选择"**分配"** 按钮。

1. 将 **"范围**"设置为将应用策略分配的 Kubernetes 群集的管理组、订阅或资源组。

   > [!NOTE]
   > 为 AKS 定义分配 Azure 策略时，**范围**必须包括 AKS 群集资源。

1. 为策略分配指定一**个名称**和说明，您可以使用这些名称和**说明**轻松识别它。

1. 将[策略强制设置为](./assignment-structure.md#enforcement-mode)以下值之一。

   - **已启用**- 在群集上强制实施策略。 违反的库伯内斯录取申请被拒绝。
   
   - **已禁用**- 不要在群集上强制实施策略。 库伯内斯的入学申请与违反不被拒绝。 合规性评估结果仍然可用。 在将新策略推出到运行群集时，"_已禁用"_ 选项有助于测试策略，因为不会拒绝具有违规的准入请求。

1. 选择“下一步”。

1. 设置**参数值**
   
   - 要从策略评估中排除 Kubernetes 命名空间，请在参数**Namespace 排除中**指定命名空间的列表。 建议排除 _：kube 系统_

1. 选择“查看 + 创建”****。

或者，使用[分配策略 - 门户](../assign-policy-portal.md)快速入门查找和分配 AKS 策略。 搜索 Kubernetes 策略定义，而不是示例"审核 vms"。

> [!IMPORTANT]
> **Kubernetes**类别中的内置策略仅用于 AKS。 有关内置策略的列表，请参阅[库伯内斯示例](../samples/built-in-policies.md#kubernetes)。

## <a name="logging"></a>Logging

### <a name="azure-policy-add-on-logs"></a>Azure 策略加载项日志

作为 Kubernetes 控制器/容器，Azure 策略加载项和网守在 AKS 群集中保留日志。 日志在 AKS 群集的 **"见解"** 页中公开。 有关详细信息，请参阅[了解容器使用 Azure 监视器的 AKS 群集性能](../../../azure-monitor/insights/container-insights-analyze.md)。

## <a name="remove-the-add-on"></a>删除加载项

要从 AKS 群集中删除 Azure 策略加载项，请使用 Azure 门户或 Azure CLI：

- Azure 门户

  1. 单击 **"所有服务**"，然后搜索并选择**库伯奈斯服务**，在 Azure 门户中启动 AKS 服务。

  1. 选择要禁用 Azure 策略加载项的 AKS 群集。

  1. 选择库伯内斯服务页面左侧**的策略（预览）。**

     ![来自 AKS 群集的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主页中，选择 **"禁用加载项**"按钮。

     ![禁用 AKS 加载项的 Azure 策略](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 策略加载项收集的诊断数据

库伯内斯的 Azure 策略加载项收集有限的群集诊断数据。 此诊断数据是与软件和性能相关的重要技术数据。 它的使用方式如下所示：

- 使 Azure 策略加载项保持最新
- 确保 Azure 策略加载项的安全、可靠、性能
- 改进 Azure 策略加载项 - 通过对加载项的使用进行聚合分析

加载项收集的信息不是个人数据。 当前收集以下详细信息：

- Azure 策略加载项代理版本
- 群集类型
- 群集区域
- 群集资源组
- 群集资源 ID
- 群集订阅 ID
- 群集操作系统（示例：Linux）
- 集群城市（示例：西雅图）
- 群集州或省（示例：华盛顿）
- 群集国家或地区（示例：美国）
- Azure 策略加载项在策略评估上的代理安装期间遇到的异常/错误
- Azure 策略加载项未安装的网守策略数

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看[策略定义结构](definition-structure.md)。
- 回顾[了解政策效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修复不合规资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
