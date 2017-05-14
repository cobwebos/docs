---
title: "适用于 Windows 的 Azure Kubernetes 群集 | Microsoft Docs"
description: "在 Azure 容器服务中部署用于 Windows 容器的 Kubernetes 群集并开始使用"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>容器服务中的 Kubernetes 和 Windows 容器入门


本文介绍如何在包含 Windows 节点的 Azure 容器服务中创建 Kubernetes 群集，以运行 Windows 容器。 首先使用 `az acs` Azure CLI 2.0 命令在 Azure 容器服务中创建 Kubernetes 群集。 然后借助 Kubernetes `kubectl` 命令行工具，开始使用从 Docker 映像生成的 Windows 容器。 

> [!NOTE]
> 对 Azure 容器服务中 Kubernetes 的 Windows 容器的支持处于预览状态。 
>



下图显示 Azure 容器服务中 Kubernetes 群集的体系结构，包括一个 Linux 主节点和两个 Windows 代理节点。 

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Linux 主节点为 Kubernetes REST API 提供服务，可由 SSH 通过端口 22 访问，或者由 `kubectl` 通过端口 443 访问。 
* Windows 代理节点分组在 Azure 可用性集中，运行用户的容器。 可以通过主节点经由 RDP SSH 隧道访问 Windows 节点。 Azure 负载均衡器规则根据公开的服务以动态方式添加到群集。



所有 VM 在相同的专用虚拟网络中，并完全可以相互访问。 所有 VM 都运行 kubelet、 Docker 和代理。

如需更多背景知识，请参阅 [Azure 容器服务简介](container-service-intro.md)和 [Kubernetes documentation](https://kubernetes.io/docs/home/)（Kubernetes 文档）。

## <a name="prerequisites"></a>先决条件
要使用 Azure CLI 2.0 创建 Azure 容器服务群集，用户必须：
* 具有一个 Azure 帐户（[获取免费试用版](https://azure.microsoft.com/pricing/free-trial/)）
* 已安装并登录到 [Azure CLI 2.0](/cli/azure/install-az-cli2)

还需要为 Kubernetes 群集做好以下准备。 可以事先准备这些条目，也可以使用 `az acs create` 命令选项在群集部署过程中自动生成它们。 

* **SSH RSA 公钥**：若要创建安全外壳 (SSH) RSA 密钥，请参阅 [macOS 和 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 或 [Windows](../virtual-machines/linux/ssh-from-windows.md) 指南。 

* **服务主体客户端 ID 和机密**：有关如何创建 Azure Active Directory 服务主体的步骤和其他信息，请参阅[关于 Kubernetes 群集的服务主体](container-service-kubernetes-service-principal.md)。

本文中的命令示例自动生成 SSH 密钥和服务主体。
  
## <a name="create-your-kubernetes-cluster"></a>创建 Kubernetes 群集

以下是用于创建群集的 Azure CLI 2.0 命令。 

### <a name="create-a-resource-group"></a>创建资源组
在 Azure 容器服务已[发布](https://azure.microsoft.com/regions/services/)的位置创建资源组。 以下命令在“westus”位置创建名为“myKubernetesResourceGroup”的资源组：

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>创建包含 Windows 代理节点的 Kubernetes 群集

使用 `az acs create` 命令与 `--orchestrator-type=kubernetes` 和 `--windows` 代理选项，在资源组中创建 Kubernetes 群集。 有关命令语法，请参阅 `az acs create` [帮助](/cli/azure/acs#create)。

以下命令为管理节点创建名为 *myKubernetesClusterName* 且 DNS 前缀为 *myPrefix* 的容器服务群集，以及访问 Windows 节点所需的指定凭据。 此版本的命令自动为 Kubernetes 群集生成 SSH RSA 密钥和服务主体。


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

数分钟后，命令完成，你会有一个可以运行的 Kubernetes 群集。

> [!IMPORTANT]
> 如果帐户没有创建 Azure AD 服务主体的权限，该命令会生成类似于`Insufficient privileges to complete the operation.`的错误。有关详细信息，请参阅[关于 Kubernetes 群集的服务主体](container-service-kubernetes-service-principal.md)。 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>使用 kubectl 连接到群集

若要从客户端计算机连接到 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)。 

如果尚未在本地安装 `kubectl`，可通过 `az acs kubernetes install-cli` 安装。 （也可从 [Kubernetes 站点](https://kubernetes.io/docs/tasks/kubectl/install/)下载。）

**Linux 或 macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> 默认情况下，此命令将 `kubectl` 二进制文件安装到 Linux 或 macOS 系统上的 `/usr/local/bin/kubectl`，或者 Windows 上的 `C:\Program Files (x86)\kubectl.exe`。 若要指定其他安装路径，请使用 `--install-location` 参数。
>
> 安装 `kubectl` 后，请确保其目录位于系统路径中，否则请将其添加到该路径。 


然后运行以下命令，将主 Kubernetes 群集配置下载到本地 `~/.kube/config` 文件：

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

此时可以从计算机访问群集。 可尝试运行：

```bash
kubectl get nodes
```

验证是否可以在群集中看到计算机列表。

![在 Kubernetes 群集中运行的节点](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>创建第一个 Kubernetes 服务

创建群集并使用 `kubectl` 进行连接后，请尝试从 Docker 容器启动 Windows 应用，并将其公开到 Internet。 以下基本示例使用 JSON 文件指定 Microsoft Internet Information Server (IIS) 容器，然后使用 `kubctl apply` 来创建它。 

1. 创建名为 `iis.json` 的本地文件，并复制以下内容。 该文件告知 Kubernetes 在 Windows Server 2016 Server Core 上运行 IIS，使用 [Docker 中心](https://hub.docker.com/r/microsoft/iis/)提供的公共映像。 该容器使用端口 80，但最初只能在群集网络中访问。

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. 若要启动该应用程序，请键入：  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. 若要跟踪容器的部署，请键入：  
  ```bash
  kubectl get pods
  ```
  当容器正在进行部署时，其状态为 `ContainerCreating`。 

  ![处于 ContainerCreating 状态的 IIS 容器](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  考虑到 IIS 映像的大小，容器可能需要数分钟才能进入`Running`状态。

  ![处于“正在运行”状态的 IIS 容器](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. 若要将容器公开给全世界，请键入以下命令：

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  有了此命令，Kubernetes 就可以使用公共 IP 地址创建 Azure 负载均衡器规则。 更改传播到负载均衡器需要几分钟时间。 有关详细信息，请参阅[对 Azure 容器服务中 Kubernetes 群集内的容器进行负载均衡](container-service-kubernetes-load-balancing.md)。

5. 运行以下命令，以便查看服务的状态。

  ```bash
  kubectl get svc
  ```

  IP 地址最初显示为`pending`：

  ![挂起的外部 IP 地址](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  几分钟后，IP 地址设置完毕：
  
  ![IIS 的外部 IP 地址](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. 外部 IP 地址可用后，即可在浏览器中浏览到它：

  ![浏览到 IIS 的图像](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. 若要删除 IIS pod，请键入：

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>后续步骤

* 若要使用 Kubernetes UI，请运行 `kubectl proxy` 命令。 然后，浏览到 http://localhost:8001/ui。

* 若要了解如何通过相关步骤来生成自定义 IIS 网站并在 Windows 容器中运行该网站，请参阅 [Docker 中心](https://hub.docker.com/r/microsoft/iis/)提供的指南。

* 若要使用 PuTTy 通过通往主节点的 RDP SSH 隧道访问 Windows 节点，请参阅 [ACS-Engine 文档](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master)。 

