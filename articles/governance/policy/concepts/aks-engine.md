---
title: 了解 Azure Policy for AKS 引擎
description: 了解 Azure 策略如何使用 CustomResourceDefinitions 并从守卫 v3 打开策略代理，使用 AKS 引擎管理群集。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436427"
---
# <a name="understand-azure-policy-for-aks-engine"></a>了解 Azure Policy for AKS 引擎

Azure 策略与[AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/README.md)（一种提供便利工具的系统）集成，可在 Azure 上快速启动自托管 Kubernetes 群集。 通过这种集成，可以集中、一致的方式为 AKS 引擎自行管理的群集提供大规模操作和安全措施。 通过扩展[开放策略代理](https://www.openpolicyagent.org/)（OPA）[守卫](https://github.com/open-policy-agent/gatekeeper)v3 （beta）的使用，Kubernetes 的_许可控制器 webhook_ ，azure 策略使你可以从一个位置管理和报告 Azure 资源和 AKS 引擎自我管理群集的符合性状态。

> [!NOTE]
> 适用于 AKS 引擎的 Azure 策略是公共预览版，没有 SLA。 此网关守卫是 Beta 版本，由开放源社区支持。 服务仅支持内置策略定义，并为配置有服务主体的每个资源组提供单个 AKS 引擎群集。

> [!IMPORTANT]
> 若要获取对 Azure Policy for AKS Engine、AKS Engine 或看门程序 v3 的支持，请在 AKS Engine GitHub 存储库中创建[新问题](https://github.com/Azure/aks-engine/issues/new/choose)。

## <a name="overview"></a>概述

若要为 Azure 上的自托管 Kubernetes 群集启用和使用 Azure Policy for AKS 引擎，请执行以下操作：

- [先决条件](#prerequisites)
- [安装 Azure 策略外接程序](#installing-the-add-on)
- [为 AKS 引擎分配策略定义](#built-in-policies)
- [等待验证](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>必备组件

在安装 Azure 策略外接程序或启用任何服务功能之前，订阅必须启用**PolicyInsights**资源提供程序，并为群集服务主体创建角色分配。 

1. 若要启用资源提供程序，请按照[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步骤操作，或者运行 Azure CLI 或 Azure PowerShell 命令：

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

   - 如果你不知道群集服务主体应用程序 ID，请在下面的命令中查找它。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 通过 Azure CLI 将 "Policy Insights 数据写入程序（预览版）" 角色分配分配给群集服务主体应用 ID （从上一步开始，值_aadClientID_ ）。 将 `<subscriptionId>` 替换为你的订阅 ID，并将 `<aks engine cluster resource group>` 替换为 AKS 引擎自托管 Kubernetes 群集所在的资源组。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure 策略附加项

适用于 Kubernetes 的_Azure 策略外接程序_将 azure 策略服务连接到网关守卫许可控制器。 安装到_kube_命名空间中的外接程序制定以下功能：

- 检查 Azure 策略以分配给 AKS 引擎群集
- 下载并安装策略详细信息、约束模板和约束
- 对 AKS 引擎群集运行完全扫描符合性检查
- 向 Azure 策略报告审核和符合性详细信息

### <a name="installing-the-add-on"></a>安装外接程序

完成先决条件后，可以安装 Azure 策略外接程序。 在 AKS 引擎的创建或更新周期中或在现有群集上进行独立操作时，安装可以是。

- 在创建或更新周期期间安装

  若要在创建新的自管理群集期间启用 Azure 策略外接程序，或者作为现有群集的更新，请包含 AKS 引擎的**加载项**属性群集定义。

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

  有关的详细信息，请参阅 external 收视指南[群集定义 AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。

- 在现有群集中安装 Helm 图表

  使用以下步骤来准备群集并安装外接程序：

  1. 将守卫安装到_网关守卫系统_命名空间。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. 向_kube 系统_添加_控制平面_标签。 此标签不包括通过网关守卫和 Azure 策略外接程序审核_kube_箱和服务。

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. 将 Kubernetes 数据（命名空间、Pod、入口、服务）与 OPA 同步。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     有关详细信息，请参阅[OPA-复制数据](https://github.com/open-policy-agent/gatekeeper#replicating-data)。

  1. 将 Azure 策略存储库添加到 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     有关详细信息，请参阅[Helm 图表-快速入门指南](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 使用 Helm 图表安装外接程序。 将 `<subscriptionId>` 替换为你的订阅 ID，并将 `<aks engine cluster resource group>` 替换为 AKS 引擎自托管 Kubernetes 群集所在的资源组。

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     有关外接程序 Helm 图安装内容的详细信息，请参阅 GitHub 上的[Azure 策略外接程序 Helm 图表定义](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts)。

     > [!NOTE]
     > 由于 Azure 策略外接程序与资源组 id 之间的关系，Azure 策略仅支持每个资源组有一个 AKS 引擎群集。

若要验证外接程序安装是否成功以及_azure 策略_pod 是否正在运行，请运行以下命令：

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>验证和报告频率

外接程序通过 Azure 策略进行检查，每5分钟进行一次策略分配更改。 在此刷新周期内，外接程序将检查更改。 这些更改触发约束模板和约束的创建、更新或删除操作。

> [!NOTE]
> 虽然_群集管理员_可能有权更改约束模板和约束，但不建议也不支持对 Azure 策略创建的约束模板或约束进行更改。 在刷新周期中进行的任何手动更改都将丢失。

加载项每5分钟一次就会调用群集的完全扫描。 在通过对群集进行尝试的更改的看门人收集完整扫描和任何实时评估的详细信息后，外接程序会将结果报告回 Azure 策略，以包含在任何 Azure 策略分配等[符合性详细信息](../how-to/get-compliance-data.md)中。 仅在审核周期内返回活动策略分配的结果。 审核结果也可以被视为失败约束的 "状态" 字段中列出的冲突。

## <a name="policy-language"></a>策略语言

用于管理 AKS 引擎的 Azure 策略语言结构遵循现有策略。 效果_EnforceOPAConstraint_用于管理 AKS 引擎群集，并使用特定于使用[OPA 约束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和网关守卫 v3 的_详细信息_属性。 有关详细信息和示例，请参阅[EnforceOPAConstraint](effects.md#enforceopaconstraint)效果。

作为_constraintTemplate_和_详细信息_的一部分，策略定义中的约束属性，Azure 策略将这些[CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) （.crd）的 uri 传递给外接程序。 Rego 是 OPA 和看门程序支持的语言，用于验证对 Kubernetes 群集的请求。 通过支持现有的 Kubernetes 管理标准，Azure 策略可以重复使用现有规则并将其与 Azure 策略配对，以获得统一的云相容性报告体验。 有关详细信息，请参阅[什么是 Rego？](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)。

## <a name="built-in-policies"></a>内置策略

若要查找使用 Azure 门户管理 AKS 引擎群集的内置策略，请执行以下步骤：

1. 在 Azure 门户中启动 Azure 策略服务。 在左窗格中选择 "**所有服务**"，然后搜索并选择 "**策略**"。

1. 在 "Azure 策略" 页的左窗格中，选择 "**定义**"。

1. 从 "类别" 下拉列表框中，使用 "**全选**" 以清除筛选器，然后选择 " **Kubernetes**"。

1. 选择策略定义，然后选择 "**分配**" 按钮。

> [!NOTE]
> 为 AKS 引擎定义分配 Azure 策略时，**作用域**必须是 AKS 引擎群集的资源组。

或者，使用 "[分配策略-门户](../assign-policy-portal.md)快速入门" 查找并分配 AKS 引擎策略。 搜索 AKS 引擎策略定义，而不是示例 "审核 vm"。

> [!IMPORTANT]
> 类别**Kubernetes**中的内置策略仅适用于 AKS 引擎。

## <a name="logging"></a>日志记录

### <a name="azure-policy-add-on-logs"></a>Azure 策略附加日志

作为 Kubernetes 控制器/容器，Azure 策略外接程序会在 AKS 引擎群集中保留日志。

若要查看 Azure 策略附加日志，请使用 `kubectl`：

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>网关守卫日志

网关守卫 `gatekeeper-system` 或 `kube-system` 命名空间中通常是网关网关_守卫，但_可能位于不同的命名空间，具体取决于部署的方式。

若要查看网关守卫日志，请使用 `kubectl`：

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

有关详细信息，请参阅 OPA 文档中的[调试网关守卫](https://github.com/open-policy-agent/gatekeeper#debugging)。

## <a name="remove-the-add-on"></a>删除外接程序

若要从 AKS 引擎群集中删除 Azure 策略外接程序和网关守卫，请使用与外接程序的安装方式相符的方法：

- 如果已安装，请在 AKS Engine 的群集定义中设置**加载项**属性：

  将_azure 策略_的**加载项**属性更改为 false 后，将群集定义重新部署到 AKS 引擎：


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- 如果安装有 Helm 图表：

  1. 删除旧约束

     目前，卸载机制仅删除该网关守卫系统，它不会删除用户创建的任何_ConstraintTemplate_、_约束_或_配置_资源，也不会删除其随附的_CRDs_。

     当网关守卫运行时，可以通过以下方式删除不需要的约束：

     - 正在删除约束资源的所有实例
     - 删除_ConstraintTemplate_资源，该资源应自动清除 _.crd_
     - 删除_配置_资源将删除已同步资源的终结器

  1. 卸载 Azure 策略外接程序
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. 卸载守卫
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 策略加载项收集的诊断数据

用于 Kubernetes 的 Azure 策略外接程序将收集有限的群集诊断数据。 此诊断数据是与软件和性能相关的重要技术数据。 它的使用方式如下所示：

- 保持 Azure 策略添加到最新状态
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