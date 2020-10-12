---
title: 如何停止监视混合 Kubernetes 群集 |Microsoft Docs
description: 本文介绍如何通过 Azure Monitor 容器来停止监视混合 Kubernetes 群集。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2754649cd990b015162be158effa2b85aa1fe27e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986042"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>如何停止监视混合群集

启用 Kubernetes 群集的监视后，如果你决定不再想要监视群集，可以使用容器 Azure Monitor 停止监视群集。 本文介绍如何为以下环境实现此目的：

- Azure 上的 AKS 引擎和 Azure Stack
- OpenShift 版本4及更高版本
- 启用了 Azure Arc 的 Kubernetes（预览版）

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

    *azmon-版本-1* 表示容器 Azure Monitor 的 helm 图表版本。

2. 若要删除图表版本，请运行以下 helm 命令。

    `helm delete <releaseName>`

    例如：

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

#### <a name="using-service-principal"></a>使用服务主体
脚本 *disable-monitoring.ps1* 使用交互式设备登录。 如果你更喜欢非交互式登录，则可以使用现有的服务主体，也可以创建一个具有所需权限的新服务主体，如 [先决条件](container-insights-enable-arc-enabled-clusters.md#prerequisites)中所述。 若要使用服务主体，你必须将 $servicePrincipalClientId、$servicePrincipalClientSecret 和 $tenantId 参数替换为你打算用于 enable-monitoring.ps1 脚本的服务主体的值。

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

例如：

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>使用 Bash

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

#### <a name="using-service-principal"></a>使用服务主体
Bash 脚本 *disable-monitoring.sh* 使用交互式设备登录。 如果你更喜欢非交互式登录，则可以使用现有的服务主体，也可以创建一个具有所需权限的新服务主体，如 [先决条件](container-insights-enable-arc-enabled-clusters.md#prerequisites)中所述。 若要使用服务主体，你必须将你打算用于 *enable-monitoring.sh* bash 脚本的服务主体的客户端 id、--客户端密码和--租户 id 值传递给。

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

例如：

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>后续步骤

如果 Log Analytics 工作区仅用于支持监视群集，并且不再需要它，则必须手动将其删除。 如果你不熟悉如何删除工作区，请参阅 [删除 Azure Log Analytics 工作区](../platform/delete-workspace.md)。
