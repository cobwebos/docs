---
title: 了解 AKS 引擎的 Azure 策略
description: 了解 Azure 策略如何使用网守 v3 中的自定义资源定义和打开策略代理使用 AKS 引擎管理群集。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436427"
---
# <a name="understand-azure-policy-for-aks-engine"></a>了解 AKS 引擎的 Azure 策略

Azure 策略与[AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/README.md)集成，该系统提供了方便的工具，可在 Azure 上快速引导自管理的 Kubernetes 群集。 这种集成支持以集中、一致的方式对 AKS 引擎自管理群集进行大规模实施和防护。 通过扩展[使用开放策略代理](https://www.openpolicyagent.org/)（OPA）[门卫](https://github.com/open-policy-agent/gatekeeper)v3（beta），这是 Kubernetes 的_准入控制器 Webhook，Azure_策略可以管理和报告 Azure 资源和 AKS 引擎自管理群集从一个位置的合规性状态。

> [!NOTE]
> AKS 引擎的 Azure 策略处于公共预览状态，没有 SLA。 网守 v3 位于 Beta 中，由开源社区提供支持。 该服务仅支持内置策略定义和每个配置服务主体的资源组的单个 AKS 引擎群集。

> [!IMPORTANT]
> 要获得对 AKS 引擎、AKS 引擎或门卫 v3 的 Azure 策略的支持，请在 AKS 引擎 GitHub 存储库中创建新[问题](https://github.com/Azure/aks-engine/issues/new/choose)。

## <a name="overview"></a>概述

要在 Azure 上启用 AKS 引擎的 Azure 策略并将其与自管理的 Kubernetes 群集一起使用，请执行以下操作：

- [先决条件](#prerequisites)
- [安装 Azure 策略加载项](#installing-the-add-on)
- [为 AKS 引擎分配策略定义](#built-in-policies)
- [等待验证](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>先决条件

在安装 Azure 策略加载项或启用任何服务功能之前，订阅必须启用**Microsoft.PolicyInsights**资源提供程序，并为群集服务主体创建角色分配。 

1. 要启用资源提供程序，请按照[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步骤操作或运行 Azure CLI 或 Azure PowerShell 命令：

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 为群集服务主体创建角色分配

   - 如果您不知道群集服务主体应用 ID，则使用以下命令查找它。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 使用 Azure CLI 将"策略见解数据编写器（预览）"角色分配分配给群集服务主体应用 ID（上一步中的值_aadClientID）。_ 替换为`<subscriptionId>`订阅 ID，并将`<aks engine cluster resource group>`AKS 引擎自管理的库伯奈斯群集替换为资源组。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure 策略加载项

库伯内斯的_Azure 策略加载项_将 Azure 策略服务连接到门卫准入控制器。 加载项安装在_kube 系统_命名空间中，将实现以下功能：

- 使用 Azure 策略检查分配给 AKS 引擎群集
- 下载并安装策略详细信息、约束模板和约束
- 在 AKS 引擎群集上运行完全扫描合规性检查
- 将审核和合规性详细信息报告回 Azure 策略

### <a name="installing-the-add-on"></a>安装加载项

完成先决条件后，可以安装 Azure 策略加载项。 安装可以是在 AKS 引擎的创建或更新周期中，也可以作为对现有群集的独立操作。

- 在创建或更新周期期间安装

  要在创建新的自托管群集期间启用 Azure 策略加载项或作为对现有群集的更新，请包括 AKS 引擎的**加载**项属性群集定义。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  有关详细信息，请参阅外部指南[AKS 引擎群集定义](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。

- 使用 Helm 图表在现有群集中安装

  使用以下步骤准备群集并安装加载项：

  1. 将网守安装到_网守系统_命名空间。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. 将_控制平面_标签添加到_kube 系统_。 此标签不包括网守和 Azure 策略加载项对_kube 系统_窗格和服务的审核。

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. 将库伯内斯数据（命名空间、Pod、入口、服务）与 OPA 同步。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     有关详细信息，请参阅[OPA - 复制数据](https://github.com/open-policy-agent/gatekeeper#replicating-data)。

  1. 将 Azure 策略回购添加到 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     有关详细信息，请参阅[头盔图 - 快速入门指南](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 使用 Helm 图表安装加载项。 替换为`<subscriptionId>`订阅 ID，并将`<aks engine cluster resource group>`AKS 引擎自管理的库伯奈斯群集替换为资源组。

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     有关附加的 Helm 图表安装内容的详细信息，请参阅 GitHub 上的[Azure 策略附加显示头盔图定义](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts)。

     > [!NOTE]
     > 由于 Azure 策略加载项和资源组 ID 之间的关系，Azure 策略仅支持每个资源组的一个 AKS 引擎群集。

要验证加载项安装是否成功以及_azure 策略_窗格是否正在运行，请运行以下命令：

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>验证和报告频率

加载项使用 Azure 策略签入每 5 分钟检查策略分配的更改。 在此刷新周期中，加载项将检查更改。 这些更改触发器创建、更新或删除约束模板和约束。

> [!NOTE]
> 虽然_群集管理员_可能有权对约束模板和约束进行更改，但不建议或支持更改 Azure 策略创建的约束模板或约束。 在刷新周期中，所做的任何手动更改都丢失。

每 5 分钟，加载项将调用群集的完整扫描。 在收集完全扫描的详细信息以及 Gatekeeper 对群集尝试的更改的任何实时评估后，加载项将结果报告回 Azure 策略，以便像任何 Azure 策略分配一样包含在[合规性详细信息](../how-to/get-compliance-data.md)中。 在审核周期中仅返回活动策略分配的结果。 审核结果也可以被视为失败约束的状态字段中列出的违规行为。

## <a name="policy-language"></a>政策语言

用于管理 AKS 引擎的 Azure 策略语言结构遵循现有策略。 效果_强制OPA约束_用于管理 AKS 引擎群集，并获取特定于使用[OPA 约束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和门卫 v3_的详细信息_属性。 有关详细信息和示例，请参阅[强制OPA约束](effects.md#enforceopaconstraint)效果。

作为策略定义中_的详细信息.约束模板_和_详细信息.约束_属性的一部分，Azure 策略将这些[自定义资源定义](https://github.com/open-policy-agent/gatekeeper#constraint-templates)（CRD） 的 URI 传递给加载项。 Rego 是 OPA 和门卫支持的语言，用于验证对 Kubernetes 群集的请求。 通过支持 Kubernetes 管理的现有标准，Azure 策略可以重用现有规则并将其与 Azure 策略配对，从而获得统一的云合规性报告体验。 有关详细信息，请参阅什么是[Rego？。](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)

## <a name="built-in-policies"></a>内置策略

要查找使用 Azure 门户管理 AKS 引擎群集的内置策略，请按照以下步骤操作：

1. 在 Azure 门户中启动 Azure 策略服务。 选择左侧窗格中的所有**服务**，然后搜索并选择 **"策略**"。

1. 在 Azure 策略页的左侧窗格中，选择 **"定义**"。

1. 在"类别"下拉列表框中，使用 **"全部选择"** 清除筛选器，然后选择**Kubernetes**。

1. 选择策略定义，然后选择"**分配"** 按钮。

> [!NOTE]
> 为 AKS 引擎定义分配 Azure 策略时，**范围**必须是 AKS 引擎群集的资源组。

或者，使用[分配策略 - 门户](../assign-policy-portal.md)快速启动来查找和分配 AKS 引擎策略。 搜索 AKS 引擎策略定义，而不是示例"审核 vms"。

> [!IMPORTANT]
> **Kubernetes**类别中的内置策略仅适用于 AKS 引擎。

## <a name="logging"></a>Logging

### <a name="azure-policy-add-on-logs"></a>Azure 策略加载项日志

作为 Kubernetes 控制器/容器，Azure 策略加载项将日志保留在 AKS 引擎群集中。

要查看 Azure 策略加载项日志，请使用`kubectl`：

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>门卫日志

网守窗格，_网守-控制器-管理器-0，_ 通常位于`gatekeeper-system`或`kube-system`命名空间中，但可以位于不同的命名空间中，具体取决于其部署方式。

要查看网守日志，请使用 ： `kubectl`

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

有关详细信息，请参阅 OPA 文档中的[调试网守](https://github.com/open-policy-agent/gatekeeper#debugging)。

## <a name="remove-the-add-on"></a>删除加载项

要从 AKS 引擎群集中删除 Azure 策略加载项和网守，请使用与加载项安装方式一致的方法：

- 如果通过在 AKS 引擎的群集定义中设置**加载项**属性进行安装：

  将_azure 策略_**的加载项**属性更改为 false 后，将群集定义重新部署到 AKS 引擎：


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- 如果随头盔图一起安装：

  1. 删除旧约束

     目前卸载机制仅删除网守系统，它不删除用户创建的任何_约束模板_、_约束_或_配置_资源，也不会删除其附带_的 CRD。_

     当网守运行时，可以通过：

     - 删除约束资源的所有实例
     - 删除_约束模板_资源，该资源应自动清理_CRD_
     - 删除_Config_资源将删除同步资源上的终结器

  1. 卸载 Azure 策略加载项
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. 卸载门卫
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
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