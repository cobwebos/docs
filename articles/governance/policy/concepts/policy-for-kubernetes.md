---
title: 预览版 - 了解适用于 Kubernetes 的 Azure Policy
description: 了解 Azure Policy 如何使用 Rego 和 Open Policy Agent 来管理在 Azure 或本地运行 Kubernetes 的群集。 这是预览功能。
ms.date: 06/12/2020
ms.topic: conceptual
ms.openlocfilehash: a044ea33f1a7710c4bb97d30cf8f11d4de2838b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373618"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>了解适用于 Kubernetes 群集的 Azure Policy（预览版）

Azure Policy 将扩展 [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3，这是一个用于 [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) 的许可控制器 Webhook，它以集中、一致的方式对群集应用大规模操作和安全措施。 借助 Azure Policy，可以从一个位置管理和报告 Kubernetes 群集的符合性状态。 该加载项制定以下功能：

- 检查 Azure Policy 服务对群集的策略分配。
- 将策略定义作为[约束模板](https://github.com/open-policy-agent/gatekeeper#constraint-templates)部署到群集中，并[约束](https://github.com/open-policy-agent/gatekeeper#constraints)自定义资源。
- 向 Azure Policy 服务报告审核和符合性详细信息。

适用于 Kubernetes 的 Azure Policy 支持以下群集环境：

- [Azure Kubernetes 服务 (AKS)](../../../aks/intro-kubernetes.md)
- [已启用 Azure Arc 的 Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> 适用于 Kubernetes 的 Azure Policy 为预览版，仅支持 Linux 节点池和内置策略定义。 内置策略定义属于“Kubernetes”类别。 不_推荐_使用**EnforceOPAConstraint**和**EnforceRegoPolicy**效果和相关**Kubernetes 服务**类别的有限预览策略定义。 请改用 "使用_审核_和_拒绝_" 作为资源提供程序模式 `Microsoft.Kubernetes.Data` 。

## <a name="overview"></a>概述

若要启用 Azure Policy 并将其用于 Kubernetes 群集，请执行以下操作：

1. 配置 Kubernetes 群集并安装加载项：
   - [Azure Kubernetes 服务 (AKS)](#install-azure-policy-add-on-for-aks)
   - [已启用 Azure Arc 的 Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS 引擎](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > 有关安装的常见问题，请参阅[排查 Azure 策略外接程序](../troubleshoot/general.md#add-on-installation-errors)问题。

1. [了解适用于 Kubernetes 的 Azure Policy 语言](#policy-language)

1. [向 Kubernetes 群集分配内置定义](#assign-a-built-in-policy-definition)

1. [等待验证](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>为 AKS 安装 Azure Policy 加载项

在安装 Azure Policy 加载项或启用任何服务功能之前，订阅必须启用“Microsoft.ContainerService”和“Microsoft.PolicyInsights”资源提供程序。

1. 需要安装并配置 Azure CLI 2.0.62 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 注册资源提供程序和预览功能。

   - Azure 门户：

     1. 注册“Microsoft.ContainerService”和“Microsoft.PolicyInsights”资源提供程序。 有关步骤，请参阅[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

     1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。 

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="在“所有服务”中搜索“策略”" border="false":::

     1. 选择“Azure Policy”页左侧的“加入预览”。

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="加入“适用于 AKS 的 Policy”预览" border="false":::

     1. 选择要添加到预览的订阅行。

     1. 选择订阅列表顶部的“选择加入”按钮。

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
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"
     
     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. 如果安装了有限预览策略定义，请在“策略(预览)”页下，删除 AKS 群集中带有“禁用”按钮的加载项。

1. AKS 群集的版本必须是 1.14 或更高版本。 使用以下脚本验证 AKS 群集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安装适用于 AKS 的 Azure CLI 预览扩展版本 0.4.0 `aks-preview`：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果以前安装了 aks-preview 扩展，请使用 `az extension update --name aks-preview` 命令安装任何更新。

完成上述先决条件步骤后，请在要管理的 AKS 群集中安装 Azure Policy 加载项。

- Azure 门户

  1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“Kubernetes 服务”，启动 AKS 服务。 

  1. 选择 AKS 群集之一。

  1. 选择“Kubernetes 服务”页面左侧的“策略(预览)”。

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="AKS 群集中的策略定义" border="false":::

  1. 在主页中，选择“启用加载项”按钮。

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="启用适用于 AKS 的 Azure Policy 加载项" border="false":::

     > [!NOTE]
     > 如果“启用加载项”按钮显示为灰色，则尚未将订阅添加到预览。 如果“禁用加载项”按钮已启用，并且显示了到 v2 的迁移警告消息，则 Gatekeepver v2 仍已安装，必须将其删除。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

若要验证加载项安装是否成功以及 azure-policy 和 gatekeeper Pod 是否正在运行，请运行以下命令 ：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

最后，通过运行此 Azure CLI 命令，并将 `<rg>` 替换为资源组名称，将 `<cluster-name>` 替换为 AKS 群集名称 `az aks show -g <rg> -n <cluster-name>`，来验证是否已安装最新的加载项。 结果应类似于以下输出，config.version 应为 `v2`：

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 安装 Azure Policy 加载项

在安装 Azure Policy 加载项或启用任何服务功能之前，订阅必须启用 Microsoft.PolicyInsights 资源提供程序并为群集服务主体创建角色分配。

1. 需要安装并配置 Azure CLI 2.0.62 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 若要启用资源提供程序，请按照[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步骤操作，或运行 Azure CLI 或 Azure PowerShell 命令：

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

1. Kubernetes 群集的版本必须是 1.14 或更高版本。

1. 安装 [Helm 3](https://v3.helm.sh/docs/intro/install/)。

1. 已为 Azure Arc 启用 Kubernetes 群集。有关详细信息，请参阅[将 Kubernetes 群集载入 Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md)。

1. 拥有已启用 Azure Arc 的 Kubernetes 群集的完全限定 Azure 资源 ID。 

1. 打开加载项的端口。 Azure Policy 加载项使用这些域和端口提取策略定义和分配，并将群集的符合性报告回 Azure Policy。

   |域 |端口 |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. 将“策略见解数据编写器(预览版)”角色分配给已启用 Azure Arc 的 Kubernetes 群集。 将 `<subscriptionId>` 替换为你的订阅 ID，将 `<rg>` 替换为已启用 Azure Arc 的 Kubernetes 群集的资源组，并将 `<clusterName>` 替换为已启用 Azure Arc 的 Kubernetes 群集的名称。 对于安装步骤，请跟踪“AppId”、“密码”和“租户”的返回值。

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azure powershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   上述命令的示例输出如下：

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

完成上述先决条件步骤后，请在已启用 Azure Arc 的 Kubernetes 群集中安装 Azure Policy 加载项：

1. 将 Azure Policy 加载项存储库添加到 Helm：

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. 使用 Helm 图表安装 Azure Policy 加载项：

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   有关加载项 Helm 图表安装内容的详细信息，请参阅 GitHub 上的 [Azure Policy 加载项 Helm 图表定义](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)。

若要验证加载项安装是否成功以及 azure-policy 和 gatekeeper Pod 是否正在运行，请运行以下命令 ：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>为 AKS 引擎安装 Azure Policy 加载项

在安装 Azure Policy 加载项或启用任何服务功能之前，订阅必须启用 Microsoft.PolicyInsights 资源提供程序并为群集服务主体创建角色分配。

1. 需要安装并配置 Azure CLI 2.0.62 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 若要启用资源提供程序，请按照[资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)中的步骤操作，或运行 Azure CLI 或 Azure PowerShell 命令：

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

1. 为群集服务主体创建角色分配。

   - 如果你不知道群集服务主体应用 ID，请使用以下命令查找。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - 使用 Azure CLI 将“策略见解数据编写器(预览版)”角色分配到群集服务主体应用 ID（上一步中的 aadClientID 值）。 将 `<subscriptionId>` 替换为订阅 ID，将 `<aks engine cluster resource group>` 替换为 AKS 引擎自托管 Kubernetes 群集所在的资源组。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

完成上述先决条件步骤后，安装 Azure Policy 加载项。 可在 AKS 引擎的创建或更新周期中进行安装，也可以作为对现有群集的独立操作进行安装。

- 在创建或更新周期期间安装

  若要在创建新的自托管群集或更新现有群集时启用 Azure Policy 加载项，请包含 AKS 引擎的[加载项](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy)属性群集定义。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  有关详细信息，请参阅外部指南 [AKS 引擎群集定义](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。

- 在现有群集中使用 Helm 图表进行安装

  使用以下步骤来准备群集并安装加载项：

  1. 安装 [Helm 3](https://v3.helm.sh/docs/intro/install/)。

  1. 将 Azure Policy 存储库添加到 Helm。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     有关详细信息，请参阅 [Helm 图表 - 快速入门指南](https://helm.sh/docs/using_helm/#quickstart-guide)。

  1. 使用 Helm 图表安装加载项。 将 `<subscriptionId>` 替换为订阅 ID，将 `<aks engine cluster resource group>` 替换为 AKS 引擎自托管 Kubernetes 群集所在的资源组。

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     有关加载项 Helm 图表安装内容的详细信息，请参阅 GitHub 上的 [Azure Policy 加载项 Helm 图表定义](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)。

     > [!NOTE]
     > 由于 Azure Policy 加载项与资源组 ID 之间的关系，Azure Policy 对每个资源组仅支持一个 AKS 引擎群集。

若要验证加载项安装是否成功以及 azure-policy 和 gatekeeper Pod 是否正在运行，请运行以下命令 ：

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Policy 语言

用于管理 Kubernetes 的 Azure Policy 语言结构遵循现有策略定义。 使用的[资源提供程序模式](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` ，会使用 "[审核](./effects.md#audit)" 和 "[拒绝](./effects.md#deny)" 来管理你的 Kubernetes 群集。 _审核_和_拒绝_必须提供特定于使用[OPA 约束框架](https://github.com/open-policy-agent/frameworks/tree/master/constraint)和网关守卫 v3 的**详细信息**属性。

作为策略定义中 details.constraintTemplate 和 details.constraint 属性的一部分，Azure Policy 将这些 [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) 的 URI 传递给加载项 。 Rego 是 OPA 和 Gatekeeper 支持的语言，用于验证对 Kubernetes 群集的请求。 通过支持 Kubernetes 管理的现有标准，Azure Policy 可重用现有规则并将其与 Azure Policy 配对以获得统一的云符合性报告体验。 有关详细信息，请参阅[什么是 Rego？](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)。

## <a name="assign-a-built-in-policy-definition"></a>分配内置策略定义

若要为 Kubernetes 群集分配策略定义，系统必须为你分配适当的基于角色的访问控制 (RBAC) 策略分配操作。 内置 RBAC 角色“资源策略参与者”和“所有者”可进行这些操作。 若要了解详细信息，请参阅 [Azure Policy 中的 RBAC 权限](../overview.md#rbac-permissions-in-azure-policy)。

通过以下步骤，使用 Azure 门户查找用于管理群集的内置策略定义：

1. 在 Azure 门户中启动 Azure Policy 服务。 在左窗格中选择“所有服务”，然后搜索并选择“策略” 。

1. 在“Azure Policy”页面的左侧窗格中，选择“定义”。

1. 从“类别”下拉列表框中，使用“全选”清除筛选器，然后选择“Kubernetes” 。

1. 选择策略定义，然后选择“分配”按钮。

1. 将“范围”设置为将应用策略分配的 Kubernetes 群集的管理组、订阅或资源组。

   > [!NOTE]    
   > 为 Kubernetes 定义分配 Azure Policy 时，“范围”必须包括群集资源。 对于 AKS 引擎群集，“范围”必须是群集的资源组。

1. 为策略分配提供可以用于轻松识别它的“名称”和“说明”。    

1. 将[策略实施](./assignment-structure.md#enforcement-mode)设置为下面的一个值    
   使用。   

   - 已启用 - 在群集上强制实施策略。 拒绝带有冲突的 Kubernetes 许可请求。    

   - 已禁用 - 不在群集上强制实施策略。 不拒绝带有冲突的 Kubernetes 许可请求。 符合性评估结果仍可用。 向运行群集推出新策略定义时，“已禁用”选项可用于测试策略定义，因为不拒绝带有冲突的许可请求。

1. 选择“**下一页**”。 

1. 设置参数值 

   - 若要从策略评估中排除 Kubernetes 命名空间，请在参数“命名空间排除”中指定命名空间的列表。 建议排除以下内容：kube-system、gatekeeper-system 和 azure-arc。

1. 选择“查看 + 创建”。

或者，使用[分配策略 - 门户](../assign-policy-portal.md)快速入门来查找和分配 Kubernetes 策略。 搜索 Kubernetes 策略定义，而不是示例“audit vms”。

> [!IMPORTANT]
> 内置策略定义适用于 Kubernetes 类别的 Kubernetes 群集。 有关内置策略定义的列表，请参阅 [Kubernetes 示例](../samples/built-in-policies.md#kubernetes)。

## <a name="policy-evaluation"></a>策略评估

加载项每 15 分钟使用 Azure Policy 服务签入一次，查看策略分配中的更改。
在此刷新周期内，加载项将检查更改。 这些更改将触发约束模板和约束的创建、更新或删除。

在 Kubernetes 群集中，如果命名空间具有以下任意一种标签，则不拒绝带有冲突的许可请求。 符合性评估结果仍可用。

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> 虽然群集管理员可能有权创建和更新 Azure Policy 加载项安装的约束模板和约束资源，但这些情况不受支持，因为手动更新会被覆盖。 Gatekeeper 会继续评估在安装加载项和分配 Azure Policy 策略定义之前已存在的策略。

每隔 15 分钟，加载项就会调用对群集的完全扫描。 在收集完全扫描的详细信息和 Gatekeeper 对群集尝试更改的所有实时评估后，加载项将结果报告回 Azure Policy，以便像所有 Azure Policy 分配一样包含在[符合性详细信息](../how-to/get-compliance-data.md)中。 在审核周期中，仅返回活动策略分配的结果。 审核结果也可以视为已失败约束的“状态”字段中列出的[冲突](https://github.com/open-policy-agent/gatekeeper#audit)。

> [!NOTE]
> 适用于 Kubernetes 群集的 Azure Policy 中的每个符合性报告都包含过去 45 分钟内的所有冲突。 时间戳指示发生冲突的时间。

## <a name="logging"></a>日志记录

作为 Kubernetes 控制器/容器，azure-policy 和 gatekeeper pod 在 Kubernetes 群集中保留日志。 日志可以在 Kubernetes 群集的“见解”页中公开。
有关详细信息，请参阅[使用适用于容器的 Azure Monitor 监视 Kubernetes 群集性能](../../../azure-monitor/insights/container-insights-analyze.md)。

若要查看加载项日志，请使用 `kubectl`：

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

有关详细信息，请参阅 Gatekeeper 文档中的[调试 Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging)。

## <a name="remove-the-add-on"></a>删除加载项

### <a name="remove-the-add-on-from-aks"></a>从 AKS 删除加载项

若要从 AKS 群集中删除 Azure Policy 加载项，请使用 Azure 门户或 Azure CLI：

- Azure 门户

  1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“Kubernetes 服务”，启动 AKS 服务。 

  1. 选择要在其中禁用 Azure Policy 加载项的 AKS 群集。

  1. 选择“Kubernetes 服务”页面左侧的“策略(预览)”。

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="AKS 群集中的策略定义" border="false":::

  1. 在主页中，选择“禁用加载项”按钮。

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="禁用适用于 AKS 的 Azure Policy 加载项" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>从已启用 Azure Arc 的 Kubernetes 中删除加载项

若要从已启用 Azure Arc 的 Kubernetes 群集中删除 Azure Policy 加载项和 Gatekeeper，请运行以下 Helm 命令：

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>从 AKS 引擎删除加载项

若要从 AKS 引擎群集中删除 Azure Policy 加载项和 Gatekeeper，请使用与加载项的安装方式一致的方法：

- 如果设置 AKS 引擎群集定义中的“加载项”属性进行安装：

  将 azure-policy 的“加载项”属性更改为 false 后，将群集定义重新部署到 AKS 引擎：


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  有关详细信息，请参阅 [AKS 引擎 - 禁用 Azure Policy 加载项](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)。

- 如果安装有 Helm 图表，请运行以下 Helm 命令：

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy 加载项收集的诊断数据

适用于 Kubernetes 的 Azure Policy 加载项收集有限的群集诊断数据。 该诊断数据是与软件和性能相关的重要技术数据。 可通过以下方式使用该数据：

- 使 Azure Policy 加载项保持最新
- 使 Azure Policy 加载项保持安全、可靠和高性能
- 改进 Azure Policy 加载项 - 通过对加载项使用的聚合分析

加载项收集的信息不是个人数据。 当前正在收集以下详细信息：

- Azure Policy 加载项代理版本
- 群集类型
- 群集区域
- 群集资源组
- 群集资源 ID
- 群集订阅 ID
- 群集 OS（示例：Linux）
- 群集城市（示例：西雅图）
- 群集州或省/自治区/直辖市（示例：华盛顿州）
- 群集国家或地区（示例：美国）
- 在策略评估的代理安装期间，Azure Policy 加载项遇到异常/错误
- Azure Policy 加载项未安装的 Gatekeeper 策略数

## <a name="next-steps"></a>后续步骤

- 查看 [Azure Policy 示例](../samples/index.md)中的示例。
- 查看[策略定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。