---
title: 快速入门 - 适用于 Windows 的 Azure Kubernetes 群集
description: 快速学习在 Azure 容器服务中使用 Azure CLI 为 Windows 容器创建 Kubernetes 群集。
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 74ce913548fbcefdc441d0d2b772c864dacd4482
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164993"
---
# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>为 Windows 容器部署 Kubernetes 群集

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详细介绍如何在 [Azure 容器服务](../container-service-intro.md)中使用 Azure CLI 部署 [Kubernetes](https://kubernetes.io/docs/home/) 群集。 部署群集后，使用 Kubernetes `kubectl` 命令行工具连接到群集，并部署第一个 Windows 容器。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

> [!NOTE]
> 对 Azure 容器服务中 Kubernetes 上 Windows 容器的支持处于预览状态。 
>

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集
使用 [az acs create](/cli/azure/acs#az_acs_create) 命令在 Azure 容器服务中创建 Kubernetes 群集。 

以下示例创建名为 myK8sCluster 的群集，其中包含一个 Linux 主节点和两个 Windows 代理节点。 此示例创建连接到 Linux 主节点所需的 SSH 密钥。 此示例使用 azureuser 作为管理用户名，使用 myPassword12 作为 Windows 节点上的密码。 更新这些值，使其适用于环境。 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

几分钟后，该命令完成并显示有关部署的信息。

## <a name="install-kubectl"></a>安装 kubectl

若要从客户端计算机连接到 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)。 

如果使用 Azure CloudShell，则 `kubectl` 已安装。 如果想在本地安装，可以使用 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 命令。

以下 Azure CLI 示例向系统安装 `kubectl`。 在 Windows 上，以管理员身份运行此命令。

```azurecli-interactive 
az acs kubernetes install-cli
```


## <a name="connect-with-kubectl"></a>连接 kubectl

若要配置 `kubectl` 以连接到 Kubernetes 群集，请运行 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令。 下面的示例下载 Kubernetes 群集的群集配置。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

若要验证从计算机到群集的连接，请尝试运行：

```azurecli-interactive
kubectl get nodes
```

`kubectl` 列出主节点和代理节点。

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```

## <a name="deploy-a-windows-iis-container"></a>部署 Windows IIS 容器

可在包含一个或多个容器的 Kubernetes Pod 内运行 Docker 容器。 

此基本示例使用 JSON 文件指定 Microsoft Internet Information Server (IIS) 容器，然后使用 `kubctl apply` 命令创建 Pod。 

创建名为 `iis.json` 的本地文件，并复制以下文本。 此文件告知 Kubernetes 使用 [Docker 中心](https://hub.docker.com/r/microsoft/iis/)提供的公共容器映像在 Windows Server 2016 Nano Server 上运行 IIS。 该容器使用端口 80，但最初只能在群集网络中访问。

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
        "image": "microsoft/iis:nanoserver",
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

若要启动 Pod，请键入：
  
```azurecli-interactive
kubectl apply -f iis.json
```  

若要跟踪部署，请键入：
  
```azurecli-interactive
kubectl get pods
```

当 Pod 正在进行部署时，状态为 `ContainerCreating`。 可能需要几分钟时间容器才能进入 `Running` 状态。

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

若要通过公共 IP 地址向世界公开 Pod，请键入以下命令：

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

通过使用此命令，Kubernetes 创建服务，并使用该服务公共 IP 地址创建 Azure 负载均衡器规则。 

运行以下命令，以便查看服务的状态。

```azurecli-interactive
kubectl get svc
```

IP 地址最初显示为 `pending`。 几分钟后，将设置 `iis` Pod 的外部 IP 地址：
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

可以使用所选 Web 浏览器在外部 IP 地址查看默认 IIS 欢迎页：

![浏览到 IIS 的图像](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>删除群集
如果不再需要群集，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>后续步骤

此快速入门中部署了 Kubernetes 群集、连接了 `kubectl` 并部署了具有 IIS 容器的 Pod。 若要详细了解 Azure 容器服务，请继续学习 Kubernetes 教程。

> [!div class="nextstepaction"]
> [管理 ACS Kubernetes 群集](container-service-tutorial-kubernetes-prepare-app.md)
