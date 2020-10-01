---
title: 如何更新用于容器的 Azure Monitor 以启用指标 | Microsoft Docs
description: 本文介绍如何更新用于容器的 Azure Monitor，以启用支持浏览聚合指标并针对其发出警报的自定义指标功能。
ms.topic: conceptual
ms.date: 09/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c420c91e20cc1cf9ab5e4f58bdd352ead3ba4d0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618139"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>如何更新用于容器的 Azure Monitor 以启用指标

容器 Azure Monitor 引入了支持从 Azure Kubernetes 服务收集指标 (AKS) 和启用了 Azure Arc 的 Kubernetes 群集节点和 pod，并将它们写入 Azure Monitor 度量值存储。 此项更改旨在更及时地呈现性能图表中的聚合计算结果（平均值、计数、最大值、最小值与总和），支持在 Azure 门户仪表板中固定性能图表，以及支持指标警报。

>[!NOTE]
>此功能当前不支持 Azure Red Hat OpenShift 群集。
>

在使用此功能的同时会启用以下指标：

| 指标命名空间 | 指标 | 说明 |
|------------------|--------|-------------|
| 见解：容器/节点 | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | *节点*度量值包括作为维度的*主机*。 它们还包括<br> 作为 *主机* 维度的值的节点名称。 |
| 见解/容器 | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | 作为 *pod* 指标，它们包括下列各项： ControllerName、Kubernetes 命名空间、名称和阶段。 |
| 见解：容器/容器 | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |

若要支持这些新功能，请在 release，版本 **microsoft/oms： ciprod05262020** for AKS and 版本 **microsoft/oms： Ciprod09252020** For Azure Arc enabled Kubernetes 群集中提供了一个新的容器化代理。 新的 AKS 部署自动包含此配置更改和功能。 更新群集以支持此功能可从 Azure 门户、Azure PowerShell 或 Azure CLI 执行。 Azure PowerShell 和 CLI。 你可以为订阅中的每个群集或所有群集启用此项。

任一进程都将 **监视指标发布者** 角色分配给群集的服务主体或用户分配的 MSI 用于监视加载项，以便可以将代理收集的数据发布到群集资源。 监视指标发布者仅有权向资源推送指标，而无法更改任何状态、更新资源或读取任何数据。 有关角色的详细信息，请参阅 [监视指标发布者角色](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)。 监视指标发布者角色要求不适用于启用了 Azure Arc 的 Kubernetes 群集。

> [!IMPORTANT]
> 对于启用了 Azure Arc 的 Kubernetes 群集，不需要升级，因为这些群集已有最低要求的代理版本。

## <a name="prerequisites"></a>先决条件

更新群集之前，请确认以下各项：

* 自定义指标只在一部分 Azure 区域中可用。 受支持的区域列表在[此处](../platform/metrics-custom-overview.md#supported-regions)记录。

* 你是 AKS 群集资源上的 **[所有者](../../role-based-access-control/built-in-roles.md#owner)** 角色的成员，这样才能收集节点和 Pod 自定义性能指标。 此要求不适用于启用了 Azure Arc 的 Kubernetes 群集。

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.59 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>通过 Azure 门户升级群集

对于用于容器的 Azure Monitor 监视的现有 AKS 群集，在从 Azure Monitor 中的多群集视图选择要查看其运行状况的群集，或者直接从左窗格中的“见解”选择该群集后，门户顶部应会显示一个横幅。

![Azure 门户中的“升级 AKS 群集”横幅](./media/container-insights-update-metrics/portal-banner-enable-01.png)

单击“启用”会启动升级群集的过程。 完成此过程可能需要几秒钟，可在菜单中的“通知”下面跟踪进度。

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>在 Azure 命令外壳中使用 Bash 升级所有群集

执行以下步骤，在 Azure 命令外壳中使用 Bash 更新订阅中的所有群集。

1. 使用 Azure CLI 运行以下命令。  使用 AKS 群集的“AKS 概述”页中的值编辑 **subscriptionId** 的值。

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    完成配置更改可能需要几秒钟。 完成后，系统会显示包含结果的消息，如下所示：

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>使用 Azure CLI 升级每个群集

执行以下步骤，使用 Azure CLI 更新订阅中的特定群集。

1. 使用 Azure CLI 运行以下命令。 使用 AKS 群集的“AKS 概述”页中的值编辑 **subscriptionId**、**resourceGroupName** 和 **clusterName** 的值。  运行命令 `az aks show` 后会返回 **clientIdOfSPN** 的值，如以下示例所示。

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    若要获取 **clientIdOfSPNOrMsi**的值，可以运行命令， `az aks show` 如以下示例中所示。 如果 **servicePrincipalProfile** 对象具有有效的 *clientid* 值，则可以使用该值。 否则，如果将其设置为 *msi*，则需要从中传入 clientid `addonProfiles.omsagent.identity.clientId` 。

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>使用 Azure PowerShell 升级所有群集

执行以下步骤，使用 Azure PowerShell 更新订阅中的所有群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) **mdm_onboarding_atscale.ps1** 脚本并将其保存到 GitHub 存储库中的本地文件夹。
2. 使用 Azure PowerShell 运行以下命令。  使用 AKS 群集的“AKS 概述”页中的值编辑 **subscriptionId** 的值。

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    完成配置更改可能需要几秒钟。 完成后，系统会显示包含结果的消息，如下所示：

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>使用 Azure PowerShell 升级每个群集

执行以下步骤，使用 Azure PowerShell 更新特定的群集。

1. [下载](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) **mdm_onboarding.ps1** 脚本并将其保存到 GitHub 存储库中的本地文件夹。

2. 使用 Azure PowerShell 运行以下命令。 使用 AKS 群集的“AKS 概述”页中的值编辑 **subscriptionId**、**resourceGroupName** 和 **clusterName** 的值。

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    完成配置更改可能需要几秒钟。 完成后，系统会显示包含结果的消息，如下所示：

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>验证更新

使用上述方法之一启动更新后，可以使用 Azure Monitor 指标资源管理器验证“指标命名空间”中是否列出了“见解”。  如果是这样，可以继续设置 [指标警报](../platform/alerts-metric.md) ，或将图表固定到 [仪表板](../../azure-portal/azure-portal-dashboards.md)。  
