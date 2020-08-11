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
ms.custom: references_regions
ms.openlocfilehash: 761263a4cb8c83475142c2afcc39695bb84d46cd
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080484"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>连接已启用 Azure Arc 的 Kubernetes 群集（预览版）

本文档介绍了如何将任何云本机计算基础 (CNCF) 认证 Kubernetes 群集（如 Azure 上的 AKS Azure Stack、GKE、EKS 和 VMware vSphere 群集）连接到 Azure Arc。

## <a name="before-you-begin"></a>开始之前

验证是否已满足以下要求：

* 已启动并正在运行的 Kubernetes 群集。 如果你没有现有的 Kubernetes 群集，则可以使用以下指南之一来创建测试群集：
  * [在 Docker 中使用 Kubernetes 创建 Kubernetes 群集 (种类) ](https://kind.sigs.k8s.io/)
  * 使用 Docker for [Mac](https://docs.docker.com/docker-for-mac/#kubernetes)或[Windows](https://docs.docker.com/docker-for-windows/#kubernetes)创建 Kubernetes 群集
* 需要一个 kubeconfig 文件来访问群集上的群集和群集管理角色，以便部署启用了 Arc 的 Kubernetes 代理。
* 使用 `az login` 和 `az connectedk8s connect` 命令的用户或服务主体必须具有对“Microsoft.Kubernetes/connectedclusters”资源类型的“读取”和“写入”权限。 "Kubernetes Cluster-Azure Arc 加入" 角色具有这些权限，可用于用户或服务主体上的角色分配。
* 使用 connectedk8s 扩展加入群集时，Helm 3 是必需的。 [安装最新版本的 Helm 3](https://helm.sh/docs/intro/install)以满足此要求。
* 安装支持 Azure Arc 的 Kubernetes CLI 扩展需要 Azure CLI 版本 2.3 +。 [安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或更新到最新版本，以确保 Azure CLI 版本 2.3 +。
* 安装启用 Arc 的 Kubernetes CLI 扩展：
  
  安装 `connectedk8s` 扩展，该扩展可帮助你将 Kubernetes 群集连接到 Azure：
  
  ```console
  az extension add --name connectedk8s
  ```
  
  安装 `k8sconfiguration` 扩展：
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  如果以后要更新这些扩展，请运行以下命令：
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

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
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | 代理的数据平面终结点，用于推送状态和提取配置信息                                      |
| `https://docker.io`                                                                                            | 拉取容器映像所需                                                                                         |
| `https://github.com`、git://github.com                                                                         | 示例 GitOps 存储库托管在 GitHub 上。 配置代理需要连接到指定的任何 git 终结点。 |
| `https://login.microsoftonline.com`                                                                            | 提取和更新 Azure 资源管理器令牌所需                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | 拉取 Azure Arc 代理的容器映像所需                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>为已启用 Azure Arc 的 Kubernetes 注册两个提供程序：

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

注册是一个异步过程。 注册可能需要大约 10 分钟。 可以使用以下命令监视注册进程：

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>创建资源组

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

你还可以在[Azure 门户](https://portal.azure.com/)上查看此资源。 在浏览器中打开门户后，根据前面在命令中使用的资源名称和资源组名称输入，导航到 "资源组" 和 "已启用 Azure Arc" Kubernetes 资源 `az connectedk8s connect` 。

> [!NOTE]
> 载入群集后，在 Azure 门户中启用了 Azure Arc Kubernetes 资源的 "概述" 页上，大约需要5到10分钟的群集元数据 (群集版本、代理版本、) 的节点数。

## <a name="connect-using-an-outbound-proxy-server"></a>使用出站代理服务器进行连接

如果群集位于出站代理服务器后面，Azure CLI 和启用了 Arc 的 Kubernetes 代理需要通过出站代理服务器路由其请求。 以下配置有助于实现此目的：

1. `connectedk8s`通过运行以下命令，检查计算机上安装的扩展的版本：

    ```bash
    az -v
    ```

    需要 `connectedk8s` 扩展版本 >= 0.2.3 来设置具有出站代理的代理。 如果你的计算机上有版本 < 0.2.3，请执行[更新步骤](#before-you-begin)以获取计算机上的最新版本的扩展。

2. 设置 Azure CLI 所需的环境变量：

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

3. 运行连接命令并指定代理参数：

    ```bash
    az connectedk8s connect -n <cluster-name> -g <resource-group> \
    --proxy-https https://<proxy-server-ip-address>:<port> \
    --proxy-http http://<proxy-server-ip-address>:<port> \
    --proxy-skip-range <excludedIP>,<excludedCIDR>
    ```

> [!NOTE]
> 1. 指定 excludedCIDR 时，请务必确保在群集中的通信对于代理不会中断。
> 2. 上述代理规范当前仅适用于 Arc 代理，不适用于 sourceControlConfiguration 中使用的 flux pod。 启用 Arc 的 Kubernetes 团队正在积极地处理此功能，不久将会提供该功能。

## <a name="azure-arc-agents-for-kubernetes"></a>适用于 Kubernetes 的 Azure Arc 代理

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

启用了 Azure Arc 的 Kubernetes 由几个在部署到 `azure-arc` 命名空间的群集中运行的代理（运算符）组成。

* `deployment.apps/config-agent`：监视群集上应用的源代码管理配置资源的已连接群集并更新符合性状态
* `deployment.apps/controller-manager`：是运算符的运算符，用于协调 Azure Arc 组件之间的交互
* `deployment.apps/metrics-agent`：收集其他 Arc 代理的指标，以确保这些代理表现出最佳性能
* `deployment.apps/cluster-metadata-operator`：收集分类元数据-群集版本、节点计数和 Azure Arc 代理版本
* `deployment.apps/resource-sync-agent`：将上面提到的群集元数据同步到 Azure
* `deployment.apps/clusteridentityoperator`：启用了 Azure Arc 的 Kubernetes 目前支持系统分配的标识。 clusteridentityoperator 维护其他代理用于与 Azure 进行通信的托管服务标识 (MSI) 证书。
* `deployment.apps/flux-logs-agent`：从源代码管理配置中部署的 flux 运算符收集日志

## <a name="delete-a-connected-cluster"></a>删除已连接的群集

可以使用 Azure CLI 或 Azure 门户删除 `Microsoft.Kubernetes/connectedcluster` 资源。


* **使用 Azure CLI 删除**：以下 Azure CLI 命令可用于启动对已启用 Azure Arc Kubernetes 资源的删除。
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  此命令删除 `Microsoft.Kubernetes/connectedCluster` Azure 中的资源和任何关联的 `sourcecontrolconfiguration` 资源。 Azure CLI 使用 helm uninstall 删除群集上运行的代理。

* **删除 Azure 门户**：在 Azure 门户上删除启用了 azure Arc 的 Kubernetes 资源会删除 `Microsoft.Kubernetes/connectedcluster` azure 中的资源和任何相关 `sourcecontrolconfiguration` 资源，但不会删除群集上运行的代理。 若要删除群集上运行的代理，请运行以下命令。

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>后续步骤

* [在连接的群集中使用 GitOps](./use-gitops-connected-cluster.md)
* [使用 Azure Policy 来控制群集配置](./use-azure-policy.md)
