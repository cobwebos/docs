---
title: 连接已启用 Azure Arc 的 Kubernetes 群集（预览版）
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 使用 Azure Arc 连接已启用 Azure Arc 的 Kubernetes 群集
keywords: Kubernetes, Arc, Azure, K8s, 容器
ms.openlocfilehash: 962b6a17743ea2beed1e16503739c55c83babbce
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860539"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>连接已启用 Azure Arc 的 Kubernetes 群集（预览版）

将 Kubernetes 群集连接到 Azure Arc。 

## <a name="before-you-begin"></a>开始之前

验证是否已满足以下要求：

* 已启动并正在运行的 Kubernetes 群集
* 你将需要使用 kubeconfig 和群集管理员访问权限进行访问。 
* 使用 `az login` 和 `az connectedk8s connect` 命令的用户或服务主体必须具有对“Microsoft.Kubernetes/connectedclusters”资源类型的“读取”和“写入”权限。 具有这些权限的“Azure Arc for Kubernetes Onboarding”角色可用于使用 Azure CLI 进行加入的用户或服务主体上的角色分配。
* 最新版本的 connectedk8s 和 k8sconfiguration 扩展 

## <a name="supported-regions"></a>支持的区域

* 美国东部
* 西欧

## <a name="network-requirements"></a>网络要求

Azure Arc 代理需要以下协议/端口/出站 URL 才能正常运行。

* 端口 443 上的 TCP --> `https://:443`
* 端口 9418 上的 TCP --> `git://:9418`

| 终结点 (DNS)                                                                                               | 说明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | 代理需要该终结点才可连接到 Azure 并注册群集                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`、`https://westeurope.dp.kubernetesconfiguration.azure.com` | 代理的数据平面终结点，用于推送状态和提取配置信息                                      |
| `https://docker.io`                                                                                            | 拉取容器映像所需                                                                                         |
| `https://github.com`、git://github.com                                                                         | 示例 GitOps 存储库托管在 GitHub 上。 配置代理需要连接到指定的任何 git 终结点。 |
| `https://login.microsoftonline.com`                                                                            | 提取和更新 Azure 资源管理器令牌所需                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | 拉取 Azure Arc 代理的容器映像所需                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 注册两个提供程序：

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

注册是一个异步过程。 注册可能需要大约 10 分钟。 可以使用以下命令监视注册进程：

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>安装 Azure CLI 扩展

安装 `connectedk8s` 扩展，该扩展可帮助你将 Kubernetes 群集连接到 Azure：

```console
az extension add --name connectedk8s
```

安装 `k8sconfiguration` 扩展：

```console
az extension add --name k8sconfiguration
```

运行以下命令，将扩展更新到最新版本。

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>创建资源组。

使用资源组来存储群集的元数据。

首先，创建资源组来保存连接的群集资源。

```console
az group create --name AzureArcTest -l EastUS -o table
```

**输出：**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>连接群集

接下来，将 Kubernetes 群集连接到 Azure。 `az connectedk8s connect` 的工作流如下所示：

1. 验证与 Kubernetes 群集的连接：通过 `KUBECONFIG`、`~/.kube/config` 或 `--kube-config`
1. 使用 Helm 3 将适用于 Kubernetes 的 Azure Arc 代理部署到 `azure-arc` 命名空间

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**输出：**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>验证已连接的群集

列出已连接的群集：

```console
az connectedk8s list -g AzureArcTest -o table
```

**输出：**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

已启用 Azure Arc 的 Kubernetes 会将几个运算符部署到 `azure-arc` 命名空间中。 可在此处查看这些部署和 Pod：

```console
kubectl -n azure-arc get deployments,pods
```

**输出：**

```console
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

## <a name="azure-arc-agents-for-kubernetes"></a>适用于 Kubernetes 的 Azure Arc 代理

启用了 Azure Arc 的 Kubernetes 由几个在部署到 `azure-arc` 命名空间的群集中运行的代理（运算符）组成。

* `deployment.apps/config-agent`：监视群集上应用的源代码管理配置资源的已连接群集并更新符合性状态
* `deployment.apps/controller-manager`：是运算符的运算符，用于协调 Azure Arc 组件之间的交互
* `deployment.apps/metrics-agent`：收集其他 Arc 代理的指标，以确保这些代理表现出最佳性能
* `deployment.apps/cluster-metadata-operator`：收集群集元数据 - 群集版本、节点计数和 Arc 代理版本
* `deployment.apps/resource-sync-agent`：将上面提到的群集元数据同步到 Azure
* `deployment.apps/clusteridentityoperator`：维护其他代理用于与 Azure 进行通信的托管服务标识 (MSI) 证书
* `deployment.apps/flux-logs-agent`：从源代码管理配置中部署的 flux 运算符收集日志

## <a name="delete-a-connected-cluster"></a>删除已连接的群集

可以使用 Azure CLI 或 Azure 门户删除 `Microsoft.Kubernetes/connectedcluster` 资源。

Azure CLI 命令 `az connectedk8s delete` 可删除 Azure 中的 `Microsoft.Kubernetes/connectedCluster` 资源。 Azure CLI 可删除 Azure 中任何关联的 `sourcecontrolconfiguration` 资源。 Azure CLI 使用 helm 卸载删除群集中的代理。

可在 Azure 门户中删除 Azure 中的 `Microsoft.Kubernetes/connectedcluster` 资源，并删除 Azure 中任何关联的 `sourcecontrolconfiguration` 资源。

要删除群集中的代理，需要运行 `az connectedk8s delete` 或 `helm uninstall azurearcfork8s`。

## <a name="next-steps"></a>后续步骤

* [在连接的群集中使用 GitOps](./use-gitops-connected-cluster.md)
* [使用 Azure Policy 来控制群集配置](./use-azure-policy.md)
