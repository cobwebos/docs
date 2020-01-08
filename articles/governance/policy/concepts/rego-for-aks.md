---
title: 了解 azure Kubernetes Service 的 Azure 策略
description: 了解 Azure 策略如何使用 Rego 并打开策略代理来管理 Azure Kubernetes 服务中的群集。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 9a4dd6bbc71c66c3ff37200ed57859b309909ae9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436392"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>了解 azure Kubernetes Service 的 Azure 策略

Azure 策略与[Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)（AKS）集成，以集中、一致的方式在群集上应用大规模操作和安全措施。
Azure 策略通过扩展了对[守卫](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated)V2 （[开放策略代理](https://www.openpolicyagent.org/)（OPA）的_许可控制器 webhook_ ）的使用，可以从一个位置管理和报告 azure 资源和 AKS 群集的符合性状态。

> [!NOTE]
> 适用于 AKS 的 Azure 策略处于有限预览版中，仅支持内置策略定义。

## <a name="overview"></a>概述

若要为 AKS 启用 Azure 策略并将其用于 AKS 群集，请执行以下操作：

- [选择加入预览版功能](#opt-in-for-preview)
- [安装 Azure 策略外接程序](#installation-steps)
- [为 AKS 分配策略定义](#built-in-policies)
- [等待验证](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>选择加入预览版

在安装 Azure 策略外接程序或启用任何服务功能之前，订阅必须启用**ContainerService**资源提供程序和**PolicyInsights**资源提供程序，并批准加入预览。 若要加入预览，请在 Azure 门户或 Azure CLI 中执行以下步骤：

- Azure 门户：

  1. 注册**ContainerService**和**PolicyInsights**资源提供程序。 有关步骤，请参阅[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

  1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。

     ![在所有服务中搜索策略](../media/rego-for-aks/search-policy.png)

  1. 选择 "Azure 策略" 页左侧的 "**加入预览版**"。

     ![加入 AKS preview 的策略](../media/rego-for-aks/join-aks-preview.png)

  1. 选择要添加到预览的订阅行。

  1. 选择订阅列表顶部的 "选择**加入**" 按钮。

- Azure CLI：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure 策略附加项

适用于 Kubernetes 的_Azure 策略外接程序_将 azure 策略服务连接到网关守卫许可控制器。 安装到_azure 策略_命名空间中的外接程序制定以下功能：

- 检查 Azure 策略以分配给 AKS 群集
- 下载并缓存策略详细信息，包括_rego_策略定义， **configmaps**
- 在 AKS 群集上运行完全扫描符合性检查
- 向 Azure 策略报告审核和符合性详细信息

### <a name="installing-the-add-on"></a>安装外接程序

#### <a name="prerequisites"></a>必备组件

在 AKS 群集中安装外接程序之前，必须安装预览扩展。 此步骤通过 Azure CLI 来完成：

1. 需要安装并配置 Azure CLI 版本2.0.62 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. AKS 群集必须是_1.10_版或更高版本。 使用以下脚本验证 AKS 群集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安装适用于 AKS 的 Azure CLI 预览扩展的版本_0.4.0_ ，`aks-preview`：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果以前安装了_aks-预览_扩展，请使用 `az extension update --name aks-preview` 命令安装任何更新。

#### <a name="installation-steps"></a>安装步骤

完成先决条件后，在要管理的 AKS 群集中安装 Azure 策略外接程序。

- Azure 门户

  1. 通过单击 "**所有服务**"，然后搜索并选择 " **Kubernetes 服务**"，在 Azure 门户中启动 AKS 服务。

  1. 选择一个 AKS 群集。

  1. 选择 Kubernetes 服务页面左侧的 "**策略（预览）** "。

     ![AKS 群集中的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主页中，选择 "**启用外接程序**" 按钮。

     ![启用 Azure Policy for AKS 外接程序](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 如果 "**启用外接程序**" 按钮灰显，则尚未将订阅添加到预览。 有关所需步骤，请参阅[选择加入预览版](#opt-in-for-preview)。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>验证和报告频率

外接程序通过 Azure 策略进行检查，每5分钟进行一次策略分配更改。 在此刷新周期内，外接程序会删除_azure 策略_命名空间中的所有_configmaps_ ，然后重新创建_configmaps_以供看门程序使用。

> [!NOTE]
> 虽然_群集管理员_可能有权使用_azure 策略_命名空间，但不建议也不支持对命名空间进行更改。 在刷新周期中进行的任何手动更改都将丢失。

加载项每5分钟一次就会调用群集的完全扫描。 在通过对群集进行尝试的更改的看门人收集完整扫描和任何实时评估的详细信息后，外接程序会将结果报告回 Azure 策略，以包含在任何 Azure 策略分配等[符合性详细信息](../how-to/get-compliance-data.md)中。 仅在审核周期内返回活动策略分配的结果。

## <a name="policy-language"></a>策略语言

用于管理 AKS 的 Azure 策略语言结构遵循现有策略。 效果_EnforceRegoPolicy_用于管理 AKS 群集，并使用特定于使用 OPA 和看门程序 v2 的_详细信息_属性。 有关详细信息和示例，请参阅[EnforceRegoPolicy](effects.md#enforceregopolicy)效果。

作为策略定义中 "_详细信息_" 属性的一部分，Azure 策略将 rego 策略的 URI 传递到该外接程序。 Rego 是 OPA 和看门程序支持的语言，用于验证或改变对 Kubernetes 群集的请求。 通过支持现有的 Kubernetes 管理标准，Azure 策略可以重复使用现有规则并将其与 Azure 策略配对，以获得统一的云相容性报告体验。 有关详细信息，请参阅[什么是 Rego？](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)。

## <a name="built-in-policies"></a>内置策略

若要查找使用 Azure 门户管理 AKS 的内置策略，请执行以下步骤：

1. 在 Azure 门户中启动 Azure 策略服务。 在左窗格中选择 "**所有服务**"，然后搜索并选择 "**策略**"。

1. 在 "Azure 策略" 页的左窗格中，选择 "**定义**"。

1. 从 "类别" 下拉列表框中，使用 "**全选**" 以清除筛选器，然后选择 " **Kubernetes 服务**"。

1. 选择策略定义，然后选择 "**分配**" 按钮。

> [!NOTE]
> 为 AKS 定义分配 Azure 策略时，**作用域**必须包括 AKS 群集资源。

或者，使用[分配策略-门户](../assign-policy-portal.md)快速入门查找并分配 AKS 策略。 搜索 Kubernetes 策略定义，而不是示例 "审核 vm"。

> [!IMPORTANT]
> 类别**Kubernetes service**中的内置策略仅适用于 AKS。

## <a name="logging"></a>日志记录

### <a name="azure-policy-add-on-logs"></a>Azure 策略附加日志

作为 Kubernetes 控制器/容器，Azure 策略外接程序会在 AKS 群集中保留日志。 日志在 AKS 群集的**Insights**页中公开。 有关详细信息，请参阅[了解 AKS 群集性能与容器 Azure Monitor](../../../azure-monitor/insights/container-insights-analyze.md)。

### <a name="gatekeeper-logs"></a>网关守卫日志

若要为新资源请求启用看门程序日志，请按照在[AKS 中启用和查看 Kubernetes 主节点日志](../../../aks/view-master-logs.md)中的步骤操作。
下面是一个示例查询，用于查看新资源请求的拒绝事件：

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

若要从网关容器中查看日志，请按照在[AKS 中启用和查看 Kubernetes 主节点日志](../../../aks/view-master-logs.md)中的步骤进行操作，并在 "**诊断设置**" 窗格中选中 " _kube-apiserver_ " 选项。

## <a name="remove-the-add-on"></a>删除外接程序

若要从 AKS 群集中删除 Azure 策略外接程序，请使用 Azure 门户或 Azure CLI：

- Azure 门户

  1. 通过单击 "**所有服务**"，然后搜索并选择 " **Kubernetes 服务**"，在 Azure 门户中启动 AKS 服务。

  1. 选择要在其中禁用 Azure 策略外接程序的 AKS 群集。

  1. 选择 Kubernetes 服务页面左侧的 "**策略（预览）** "。

     ![AKS 群集中的策略](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主页中，选择 "**禁用外接程序**" 按钮。

     ![禁用 AKS 外接程序的 Azure 策略](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 策略加载项收集的诊断数据

用于 Kubernetes 的 Azure 策略外接程序将收集有限的群集诊断数据。 此诊断数据是与软件和性能相关的重要技术数据。 它的使用方式如下所示：

- 将 Azure 策略添加到最新
- 保持 Azure 策略附加安全安全、可靠、高性能
- 改进 Azure 策略附加项-通过聚合分析来使用外接程序

外接程序收集的信息不是个人数据。 当前正在收集以下详细信息：

- Azure 策略附加代理版本
- 群集类型
- 群集区域
- 群集资源组
- 群集资源 ID
- 群集订阅 ID
- 群集操作系统（示例： Linux）
- 分类城（示例：西雅图）
- 群集省/直辖市/自治区（例如：华盛顿州）
- 群集国家或地区（示例：美国）
- 在策略评估过程中安装代理期间 Azure 策略外接程序遇到的异常/错误
- Azure 策略外接程序未安装的网关守卫策略数

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看[策略定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- 了解如何以[编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取相容性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不合规的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。