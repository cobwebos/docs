---
title: 如何停止监视混合 Kubernetes 群集 |Microsoft Docs
description: 本文介绍如何通过 Azure Monitor 容器来停止监视混合 Kubernetes 群集。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 86a774737d5269d77c4053ad61ab870b13288aa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885852"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>如何停止监视混合群集

启用 Kubernetes 群集的监视后，如果你决定不再想要监视群集，可以使用容器 Azure Monitor 停止监视群集。 本文介绍如何为以下环境实现此目的：

- Azure 上的 AKS 引擎和 Azure Stack
- OpenShift 版本4及更高版本
- 已启用 Azure Arc 的 Kubernetes（预览版）

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止监视

以下步骤适用于以下环境：

- Azure 上的 AKS 引擎和 Azure Stack
- OpenShift 版本4及更高版本

1. 若要首先确定群集上安装的 helm 图表版本的 Azure Monitor，请运行以下 helm 命令。

    ```
    helm list
    ```

    输出如下所示：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-版本-1*表示容器 Azure Monitor 的 helm 图表版本。

2. 若要删除图表版本，请运行以下 helm 命令。

    `helm delete <releaseName>`

    示例：

    `helm delete azmon-containers-release-1`

    这将从群集中删除发布。 可以通过运行 `helm list` 以下命令进行验证：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

配置更改可能需要几分钟才能完成。 因为 Helm 会在你删除发布后对其进行跟踪，因此你可以审核群集的历史记录，甚至还可以使用撤消删除发布 `helm rollback` 。

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>如何在启用 Arc 的 Kubernetes 上停止监视

### <a name="using-powershell"></a>使用 PowerShell

1. 使用以下命令将脚本下载并保存到使用监视外接程序配置群集的本地文件夹：

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. `$azureArcClusterResourceId`通过设置的相应值来配置变量 `subscriptionId` ， `resourceGroupName` 并 `clusterName` 表示启用了 Azure Arc 的 Kubernetes 群集资源的资源 ID。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `$kubeContext`通过运行命令，配置具有群集的**kube 上下文**的变量 `kubectl config get-contexts` 。 如果要使用当前上下文，请将值设置为 `""` 。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 运行以下命令停止监视群集。

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>使用 bash

1. 使用以下命令将脚本下载并保存到使用监视外接程序配置群集的本地文件夹：

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. `azureArcClusterResourceId`通过设置的相应值来配置变量 `subscriptionId` ， `resourceGroupName` 并 `clusterName` 表示启用了 Azure Arc 的 Kubernetes 群集资源的资源 ID。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubeContext`通过运行命令，配置具有群集的**kube 上下文**的变量 `kubectl config get-contexts` 。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 若要停止监视群集，可以根据部署方案提供不同的命令。

    运行以下命令，使用当前上下文停止监视群集。

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    运行以下命令，通过指定上下文停止监视群集

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>后续步骤

如果 Log Analytics 工作区仅用于支持监视群集，并且不再需要它，则必须手动将其删除。 如果你不熟悉如何删除工作区，请参阅[删除 Azure Log Analytics 工作区](../../log-analytics/log-analytics-manage-del-workspace.md)。
