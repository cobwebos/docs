---
title: 配置适用于容器 Azure Monitor 的 Azure Red Hat OpenShift 群集 |Microsoft Docs
description: 本文介绍如何使用 Azure Red Hat OpenShift 上托管 Azure Monitor 来配置监视 Kubernetes 群集。
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250693"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>配置适用于容器 Azure Monitor 的 Azure Red Hat OpenShift 群集

容器 Azure Monitor 为 Azure Kubernetes 服务（AKS）和 AKS 引擎群集提供丰富的监视体验。 本文介绍如何启用对[Azure Red Hat OpenShift](../../openshift/intro-openshift.md)上托管的 Kubernetes 群集的监视，以实现类似的监视体验。

>[!NOTE]
>目前，对 Azure Red Hat OpenShift 的支持是公共预览版中的一项功能。
>

可以使用以下受支持的方法，为新的或 Azure Red Hat OpenShift 的一个或多个现有部署启用容器 Azure Monitor：

- 对于来自 Azure 门户或使用 Azure 资源管理器模板的现有群集。
- 对于使用 Azure 资源管理器模板的新群集，或在使用[Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)创建新群集时使用。

## <a name="supported-and-unsupported-features"></a>支持和不支持的功能

容器 Azure Monitor 支持按[概述](container-insights-overview.md)一文中所述监视 Azure Red Hat OpenShift，但以下功能除外：

- 实时数据（预览）
- [收集](container-insights-update-metrics.md)群集节点和 pod 中的指标，并将其存储在 Azure Monitor 度量值数据库中

## <a name="prerequisites"></a>必备条件

- 若要启用和访问容器 Azure Monitor 中的功能，至少需要是 Azure 订阅中的 Azure*参与者*角色的成员，以及配置了 Azure Monitor for 容器的 Log Analytics 工作区的[*Log Analytics 参与者*](../platform/manage-access.md#manage-access-using-azure-permissions)角色的成员。

- 若要查看监视数据，你是使用为容器 Azure Monitor 配置的 Log Analytics 工作区的 " [*Log Analytics 读取*](../platform/manage-access.md#manage-access-using-azure-permissions)者角色" 权限的成员。

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板启用新群集

执行以下步骤以部署启用了监视功能的 Azure Red Hat OpenShift 群集。 继续之前，请查看教程[创建 Azure Red Hat OpenShift 群集](../../openshift/tutorial-create-cluster.md#prerequisites)，了解需要配置哪些依赖项，以便正确设置环境。

此方法包含两个 JSON 模板。 一个模板指定用于部署启用监视的群集的配置，另一个模板包含配置为指定以下项的参数值：

- Azure Red Hat OpenShift 群集资源 ID。

- 部署群集的资源组。

- 执行步骤创建一个或多个已创建的[Azure Active Directory 租户 ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) 。

- [Azure Active Directory 客户端应用程序 ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) ，请在执行步骤创建一个或多个已创建的步骤之后记下。

- [Azure Active Directory 客户端密码](../../openshift/howto-aad-app-configuration.md#create-a-client-secret)，请在执行步骤创建一个或多个已创建的步骤后注明。

- [Azure AD 安全组](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group)在执行步骤创建一个或多个已创建的步骤后注明。

- 现有 Log Analytics 工作区的资源 ID。

- 要在群集中创建的主节点数。

- 代理池配置文件中的计算节点数。

- 代理池配置文件中的基础结构节点数。

如果不熟悉使用模板部署资源的概念，请参阅：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 版本2.0.65 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

使用 Azure PowerShell 或 CLI 启用监视之前，必须先创建 Log Analytics 工作区。 若要创建工作区，可通过 [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或在 [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)中进行设置。

1. 下载并保存到本地文件夹、Azure 资源管理器模板和参数文件，使用以下命令通过监视外接程序创建群集：

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. 登录 Azure

    ```azurecli
    az login    
    ```

    如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

3. 为群集创建资源组（如果尚未创建）。 有关在 Azure 上支持 OpenShift 的 Azure 区域的列表，请参阅[支持的区域](../../openshift/supported-resources.md#azure-regions)。

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. 编辑 JSON 参数文件**newClusterWithMonitoringParam**并更新以下值：

    - *location*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. 以下步骤使用 Azure CLI 在启用了监视的情况下部署群集。

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    输出如下所示：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>为现有群集启用

执行以下步骤以启用对 Azure 中部署的 Azure Red Hat OpenShift 群集的监视。 可以从 Azure 门户或使用所提供的模板来实现此目的。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 "Azure 门户" 菜单或从 "主页" 中，选择 " **Azure Monitor**"。 在“见解”部分，选择“容器”。

3. 在“监视 - 容器”页上，选择“非监视群集”。

4. 在非监视群集列表中，找到列表中的群集，然后单击 "**启用**"。 您可以通过在列**分类类型**下查找值**ARO**来识别列表中的结果。

5. 在“载入到用于容器的 Azure Monitor”页上，如果现有 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  
    此列表 preselects 在订阅中部署群集的默认工作区和位置。

    ![为非监视群集启用监视](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >如果想要创建新的 Log Analytics 工作区用于存储来自群集的监视数据，请按照[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)中的说明进行操作。 请确保在部署 RedHat OpenShift 群集的同一订阅中创建工作区。

启用监视后，可能需要约 15 分钟才能查看群集的运行状况指标。

### <a name="enable-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板启用

此方法包含两个 JSON 模板。 一个模板指定用于启用监视的配置，另一个模板包含参数值，通过配置这些参数值可指定：

- Azure RedHat OpenShift 群集资源 ID。

- 部署群集的资源组。

- Log Analytics 工作区。

如果不熟悉使用模板部署资源的概念，请参阅：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)

- [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 版本2.0.65 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

使用 Azure PowerShell 或 CLI 启用监视之前，必须先创建 Log Analytics 工作区。 若要创建工作区，可通过 [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) 或在 [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)中进行设置。

1. 下载模板和参数文件，使用以下命令通过监视外接程序更新群集：

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. 登录 Azure

    ```azurecli
    az login    
    ```

    如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

3. 指定 Azure RedHat OpenShift 群集的订阅。

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. 运行以下命令以确定群集位置和资源 ID：

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. 编辑 JSON 参数文件**existingClusterParam**并更新值*araResourceId*和*araResoruceLocation*。 **workspaceResourceId** 的值是 Log Analytics 工作区的完整资源 ID，其中包含工作区名称。

6. 若要 Azure CLI 部署，请运行以下命令：

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    输出如下所示：

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>后续步骤

- 启用监视功能以收集 RedHat OpenShift 群集的运行状况和资源利用率，并了解[如何使用](container-insights-analyze.md)容器 Azure Monitor。

- 若要了解如何停止监视包含容器 Azure Monitor 的群集，请参阅[如何停止监视 Azure Red Hat OpenShift 群集](container-insights-optout-openshift.md)。
