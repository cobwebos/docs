---
title: 为容器 Azure Monitor 配置 Azure Red Hat OpenShift v4 |Microsoft Docs
description: 本文介绍如何使用 Azure Red Hat OpenShift 版本4或更高版本上托管 Azure Monitor 来配置 Kubernetes 群集的监视。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e9f31d1b71122c53a67dc40af31d33255e2e98d8
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994547"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>为容器配置 Azure Red Hat OpenShift v4. x Azure Monitor

为容器 Azure Monitor 提供丰富的监视体验，适用于 Azure Kubernetes 服务 (AKS) 和 AKS 引擎群集。 本文介绍如何通过启用对 [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 版本4.x 上托管的 Kubernetes 群集的监视来实现类似的监视体验。

>[!NOTE]
>目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
>

可以使用本文中所述的受支持方法，为 Azure Red Hat OpenShift v4 的一个或多个现有部署启用容器 Azure Monitor。

对于现有群集，请 [在 Azure CLI 中运行此 Bash 脚本](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create&preserve-view=true)。

## <a name="supported-and-unsupported-features"></a>支持和不支持的功能

容器 Azure Monitor 支持监视 Azure Red Hat OpenShift v4，如 [容器的 Azure Monitor 概述](container-insights-overview.md)中所述，以下功能除外：

- 实时数据 (预览) 
- 从群集节点和 pod[收集指标](container-insights-update-metrics.md)并将其存储在 Azure Monitor 度量值数据库中

## <a name="prerequisites"></a>先决条件

- Azure CLI 版本2.0.72 或更高版本  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI 工具

- [Bash 版本4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令行工具

- [Log Analytics 工作区](../platform/design-logs-deployment.md)。

    用于容器的 Azure Monitor 支持在 Azure [产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) 中列出的区域中的 Log Analytics 工作区。 若要创建你自己的工作区，可通过 [Azure 资源管理器](../samples/resource-manager-workspace.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或 [Azure 门户](../learn/quick-create-workspace.md)进行创建。

- 若要启用和访问容器 Azure Monitor 中的功能，你至少需要具有 Azure 订阅中的 Azure *参与者* 角色和 Log Analytics 工作区中的 " [*Log Analytics 参与者*](../platform/manage-access.md#manage-access-using-azure-permissions) " 角色，并配置了 "Azure Monitor" 作为容器。

- 若要查看监视数据，需要在 Log Analytics 工作区（该工作区为容器配置了 Azure Monitor）中拥有 [Log Analytics 读者](../platform/manage-access.md#manage-access-using-azure-permissions)角色。

## <a name="enable-monitoring-for-an-existing-cluster"></a>为现有群集启用监视

若要启用对 azure Red Hat OpenShift 版本4或更高版本群集（使用提供的 Bash 脚本部署在 Azure 中）的监视，请执行以下操作：

1. 通过运行以下命令登录到 Azure：

    ```azurecli
    az login
    ```

1. 通过运行以下命令，下载并保存到本地文件夹使用监视外接程序配置群集的脚本：

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. 若要确定群集的 *kubeContext* ，请运行以下命令

    ```
    adminUserName=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminUsername' -o tsv)
    adminPassword=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminPassword' -o tsv)
    apiServer=$(az aro show -g $clusterResourceGroup -n $clusterName --query apiserverProfile.url -o tsv)
    oc login $apiServer -u $adminUserName -p $adminPassword
    # openshift project name for azure monitor for containers
    openshiftProjectName="azure-monitor-for-containers"
    oc new-project $openshiftProjectName
    # get the kube config context
    kubeContext=$(oc config current-context)
    ```

1. 复制值供以后使用。

### <a name="integrate-with-an-existing-workspace"></a>与现有工作区集成

在本部分中，将使用先前下载的 Bash 脚本来启用群集监视。 若要与现有 Log Analytics 工作区集成，请首先标识参数所需的 Log Analytics 工作区的完整资源 ID `logAnalyticsWorkspaceResourceId` ，然后运行命令，针对指定的工作区启用监视外接程序。

如果没有要指定的工作区，则可以跳到 " [与默认工作区集成](#integrate-with-the-default-workspace) " 部分，让脚本为你创建新的工作区。

1. 通过运行以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

    输出将如下所示：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. 复制 **SubscriptionId** 的值。

1. 通过运行以下命令切换到承载 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. 通过运行以下命令，以默认 JSON 格式显示订阅中的工作区列表：

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. 在输出中，找到工作区名称，然后在字段 **ID** 下复制该 Log Analytics 工作区的完整资源 ID。

1. 若要启用监视，请运行以下命令。 替换 `azureAroV4ClusterResourceId` 、和参数的值 `logAnalyticsWorkspaceResourceId` `kubeContext` 。

    ```bash
    export azureAroV4ClusterResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>”
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    export kubeContext="<kubeContext name of your ARO v4 cluster>"  
    ```

    示例：

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext --workspace-id $logAnalyticsWorkspaceResourceId`

启用监视后，可能需要大约15分钟的时间才能查看群集的运行状况指标。

### <a name="integrate-with-the-default-workspace"></a>与默认工作区集成

在本部分中，将使用已下载的 Bash 脚本为 Azure Red Hat OpenShift v4 群集启用监视。

在此示例中，无需预先创建或指定现有工作区。 此命令通过在群集订阅的默认资源组中创建默认工作区（如果该区域中不存在一个工作区）来简化此过程。

创建的默认工作区的格式为*形式 defaultworkspace- \<GUID> - \<Region> *。  

替换和参数的值 `azureAroV4ClusterResourceId` `kubeContext` 。

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
export kubeContext="<kubeContext name of your ARO v4 cluster>"
```

例如：

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext`

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="enable-monitoring-from-the-azure-portal"></a>从 Azure 门户启用监视

容器 Azure Monitor 中的多群集视图突出显示 Azure Red Hat OpenShift 群集，这些群集未在 "未监视的 **群集** " 选项卡下启用监视功能。群集旁边的 " **启用** " 选项不会从门户中启动监视的载入。 你将重定向到本文，以按照本文前面所述的步骤手动启用监视。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格或从主页中，选择 " **Azure Monitor**"。

1. 在 " **见解** " 部分，选择 " **容器**"。

1. 在“监视 - 容器”页上，选择“未监视的群集” 。

1. 在非监视群集列表中，选择该群集，然后选择 " **启用**"。

    您可以通过在 "**群集类型**" 列中查找**ARO**值来确定列表中的结果。 选择 " **启用**" 后，会重定向到本文。

## <a name="next-steps"></a>后续步骤

- 现在，你已启用监视以收集 RedHat OpenShift 版本4.x 群集的运行状况和资源利用率，以及在这些群集上运行的工作负荷，了解 [如何使用](container-insights-analyze.md) 容器 Azure Monitor。

- 默认情况下，容器化代理收集所有命名空间中运行的所有容器（kube 除外）的 *stdout* 和 *stderr* 容器日志。 若要配置特定于特定命名空间或命名空间的容器日志集合，请查看 [Container Insights 代理配置](container-insights-agent-config.md) ，为 *ConfigMap* 配置文件配置所需的数据收集设置。

- 若要从群集中擦除和分析 Prometheus 指标，请查看 [配置 Prometheus 度量值抓取](container-insights-prometheus-integration.md)。

- 若要了解如何使用 Azure Monitor 容器停止监视群集，请参阅 [如何停止监视 Azure Red Hat OpenShift 群集](./container-insights-optout-openshift-v3.md)。