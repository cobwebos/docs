---
title: 排查已启用 Azure Arc 的 Kubernetes 的常见问题（预览版）
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 排查已启用 Arc 的 Kubernetes 群集的常见问题。
keywords: Kubernetes, Arc, Azure, 容器
ms.openlocfilehash: 4a8f4c652f1ab73e0b9979f77d7de5014c8d31a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540602"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>已启用 Azure Arc 的 Kubernetes 故障排除（预览版）

本文档提供了有关连接性、权限和代理方面的一些常见的故障排除方案。

## <a name="general-troubleshooting"></a>常规故障排除

### <a name="azure-cli-set-up"></a>Azure CLI 设置
使用 az connectedk8s 或 az k8sconfiguration CLI 命令之前，请确保将 az 设置为对应合适的 Azure 订阅。

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>azure-arc 代理
已启用 Azure Arc 的 Kubernetes 的所有代理在 `azure-arc` 命名空间中部署为 Pod。 在正常操作下，所有 Pod 应该运行并传递其运行状况检查。

首先，请验证 Azure Arc helm 版本：

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

如果 Helm 版本找不到或缺失，请尝试重新加入群集。

如果 Helm 版本存在并且 `STATUS: deployed` 使用 `kubectl` 确定代理的状态：

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

所有 Pod 应将 `STATUS` 显示为 `Running`，且 `READY` 应为 `3/3` 或 `2/2`。 提取日志，并描述返回 `Error` 或 `CrashLoopBackOff` 的 Pod。 如果这些 pod 中有任何一个处于粘滞 `Pending` 状态，则可能是由于群集节点上的资源不足造成的。 [向上缩放群集](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#resizing-a-cluster) 会使这些 pod 转换到 `Running` 状态。

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>将 Kubernetes 群集连接到 Azure Arc

将群集连接到 Azure 需要对 Azure 订阅的访问权限和对目标群集的 `cluster-admin` 访问权限。 如果无法访问群集或权限不足，将无法加入。

### <a name="insufficient-cluster-permissions"></a>群集权限不足

如果提供的 kubeconfig 文件没有足够的权限来安装 Azure Arc 代理，则 Azure CLI 命令在尝试调用 Kubernetes API 时将返回一个错误。

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

群集所有者应使用具有群集管理员权限的 Kubernetes 用户。

### <a name="installation-timeouts"></a>安装超时

Azure Arc 代理安装需要在目标群集上运行一组容器。 如果群集在网速缓慢的 Internet 连接上运行，则容器映像请求可能需要比 Azure CLI 超时更长的时间。

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm 问题

Helm `v3.3.0-rc.1` 版本存在一个 [问题](https://github.com/helm/helm/pull/8527) ，其中，Helm 安装/升级 (在 connectedk8s CLI 扩展的内部使用) 导致所有挂钩运行，导致以下错误：

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

若要从此问题恢复，请执行以下步骤：

1. 删除 Azure 门户中问题的 Azure Arc 已启用 Kubernetes 资源。
2. 在计算机上运行以下命令：
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. 在计算机上[安装稳定版本](https://helm.sh/docs/intro/install/)的 Helm 3，而不是预发行版。
4. 运行 `az connectedk8s connect` 具有适当值的命令，将群集连接到 Azure Arc。

## <a name="configuration-management"></a>配置管理

### <a name="general"></a>常规
为了帮助解决源代码管理配置的相关问题，请运行带有 --debug 开关的 az 命令。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>创建源代码管理配置
对于创建 KubernetesConfiguration/sourceControlConfiguration 资源而言，对 Microsoft.Kubernetes/connectedCluster 资源的参与者角色是必需的并且足以胜任。

### <a name="configuration-remains-pending"></a>配置保留 `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>监视

容器 Azure Monitor 要求在特权模式下运行 DaemonSet。 若要成功设置用于监视的规范 Charmed Kubernetes 群集，请运行以下命令：

```console
juju config kubernetes-worker allow-privileged=true
```