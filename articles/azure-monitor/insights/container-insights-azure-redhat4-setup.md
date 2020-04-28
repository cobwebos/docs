---
title: 为容器 Azure Monitor 配置 Azure Red Hat OpenShift v4 |Microsoft Docs
description: 本文介绍如何使用 Azure Red Hat OpenShift 版本4和更高版本上托管 Azure Monitor 来配置监视 Kubernetes 群集。
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196291"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>为容器配置 Azure Red Hat OpenShift v4. x Azure Monitor

容器 Azure Monitor 为 Azure Kubernetes 服务（AKS）和 AKS 引擎群集提供丰富的监视体验。 本文介绍如何启用对[Azure Red Hat OpenShift](../../openshift/intro-openshift.md)版本4.x 上托管的 Kubernetes 群集的监视，以实现类似的监视体验。

>[!NOTE]
>目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
>

可以使用以下受支持的方法为 Azure Red Hat OpenShift v4 的一个或多个现有部署启用容器 Azure Monitor：

- 对于使用提供的 bash 脚本并在[Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)中运行的现有群集。

## <a name="supported-and-unsupported-features"></a>支持和不支持的功能

容器 Azure Monitor 支持监视 Azure Red Hat OpenShift v4，如[概述](container-insights-overview.md)一文中所述，以下功能除外：

- 实时数据（预览）
- [收集](container-insights-update-metrics.md)群集节点和 pod 中的指标，并将其存储在 Azure Monitor 度量值数据库中

## <a name="prerequisites"></a>先决条件

- Azure CLI 版本2.0.72 或更高版本

- [Helm 3](https://helm.sh/docs/intro/install/)CLI 工具

- [Bash 版本4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)命令行工具

- 若要启用和访问容器 Azure Monitor 中的功能，至少需要是 Azure 订阅中的 Azure*参与者*角色的成员，以及配置了 Azure Monitor for 容器的 Log Analytics 工作区的[*Log Analytics 参与者*](../platform/manage-access.md#manage-access-using-azure-permissions)角色的成员。

- 若要查看监视数据，你是使用为容器 Azure Monitor 配置的 Log Analytics 工作区的 " [*Log Analytics 读取*](../platform/manage-access.md#manage-access-using-azure-permissions)者角色" 权限的成员。

## <a name="enable-for-an-existing-cluster"></a>为现有群集启用

执行以下步骤，以使用提供的 bash 脚本来监视在 Azure 中部署的 Azure Red Hat OpenShift 版本4和更高版本的群集。

1. 登录 Azure

    ```azurecli
    az login
    ```

2. 使用以下命令将脚本下载并保存到使用监视外接程序配置群集的本地文件夹：

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. 若要确定群集的**kube 上下文**，请在群集成功`oc login`完成后，运行命令`kubectl config current-context`并复制值。

### <a name="integrate-with-an-existing-workspace"></a>与现有工作区集成

以下步骤启用使用之前下载的 bash 脚本监视群集。 若要与现有 Log Analytics 工作区集成，请执行以下步骤以首先标识`workspaceResourceId`参数所需的 Log Analytics 工作区的完整资源 ID，然后运行命令针对指定的工作区启用监视外接程序。 如果没有要指定的工作区，则可以跳到步骤5，让脚本为你创建新的工作区。

1. 使用以下命令列出你有权访问的所有订阅：

    ```azurecli
    az account list --all -o table
    ```

    输出如下所示：

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    复制 **SubscriptionId** 的值。

2. 使用以下命令切换到托管 Log Analytics 工作区的订阅：

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 以下示例以默认 JSON 格式显示订阅中的工作区列表。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    在输出中找到工作区名称，然后将该 Log Analytics 工作区的完整资源 ID 复制到字段**id**下。

4. 运行以下命令以启用监视，同时替换`workspaceResourceId`参数的值： 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    示例：

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="integrate-with-default-workspace"></a>与默认工作区集成

以下步骤启用了使用已下载的 bash 脚本监视 Azure Red Hat OpenShift 的群集。 在此示例中，不需要预先创建或指定现有的工作区。 此命令通过在群集订阅的默认资源组中创建默认工作区（如果该区域中不存在一个）来简化此过程。 创建的默认工作区的格式类似于 *DefaultWorkspace-\<GUID>-\<Region>*。  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

容器 Azure Monitor 中的多群集视图突出显示未在 "**未监视的群集**" 选项卡下启用监视功能的 Azure Red Hat OpenShift 群集。群集旁边的 "**启用**" 选项不会从门户启动监视的载入。 你将重定向到本文，以便按照本文前面的步骤手动启用监视。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 "Azure 门户" 菜单或从 "主页" 中，选择 " **Azure Monitor**"。 在“见解”部分，选择“容器”。********

3. 在“监视 - 容器”页上，选择“非监视群集”********。

4. 在非监视群集列表中，找到列表中的群集，然后单击 "**启用**"。 您可以通过在列**分类类型**下查找值**ARO**来识别列表中的结果。 单击 "**启用**" 后，会重定向到本文。

## <a name="next-steps"></a>后续步骤

- 启用监视功能以收集 RedHat OpenShift 版本4.x 群集的运行状况和资源利用率，并了解[如何使用](container-insights-analyze.md)容器 Azure Monitor。

- 默认情况下，容器化代理收集所有命名空间（kube 除外）中运行的所有容器的 stdout/stderr 容器日志。 若要配置特定于特定命名空间或命名空间的容器日志集合，请查看[Container Insights 代理配置](container-insights-agent-config.md)，将所需的数据收集设置配置为 ConfigMap 配置文件。

- 若要从群集中擦除和分析 Prometheus 指标，请查看[配置 Prometheus 指标抓取](container-insights-prometheus-integration.md)

- 若要了解如何停止监视包含容器 Azure Monitor 的群集，请参阅[如何停止监视 Azure Red Hat OpenShift 群集](container-insights-optout-openshift.md)。
