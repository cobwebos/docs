---
title: "快速入门 - 适用于 Linux 的 Azure Kubernetes 群集"
description: "快速学习在 Azure 容器服务中使用 Azure CLI 为 Linux 容器创建 Kubernetes 群集。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: b9e62611e21450885b20d0e0b261777e13820626
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>为 Linux 容器部署 Kubernetes 群集

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

在本快速入门中，将使用 Azure CLI 部署 Kubernetes 群集。 然后，在群集上部署和运行包含 Web 前端和 Redis 实例的多容器应用程序。 完成后，即可通过 Internet 访问应用程序。 

在本文档中使用的示例应用程序是以 Python 编写的。 此处详述的概念和步骤可以用来将任何容器映像部署到 Kubernetes 群集中。 [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git) 上提供了与此项目相关的代码、Dockerfile 和预先创建的 Kubernetes 清单文件。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-vote.png)

本快速入门假定你对 Kubernetes 概念有一个基本的了解。有关 Kubernetes 的详细信息，请参阅 [Kubernetes documentation]( https://kubernetes.io/docs/home/)（Kubernetes 文档）。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑组。 

以下示例在“westeurope”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location westeurope
```

输出：

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集

使用 [az acs create](/cli/azure/acs#az_acs_create) 命令在 Azure 容器服务中创建 Kubernetes 群集。 以下示例创建名为 myK8sCluster 的群集，其中包含一个 Linux 主节点和三个 Linux 代理节点。

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

在某些情况下（如限时试用），Azure 订阅对 Azure 资源的访问受限。 如果由于可用核心有限而导致部署失败，请将 `--agent-count 1` 添加到 [az acs creat](/cli/azure/acs#az_acs_create) 命令中，以减少默认代理计数。 

几分钟后，该命令完成并返回有关群集的 JSON 格式信息。 

## <a name="connect-to-the-cluster"></a>连接至群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)。 

如果使用的是 Azure CloudShell，则 kubectl 已安装。 如果想在本地安装，可以使用 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 命令。

若要配置 kubectl 以连接到 Kubernetes 群集，请运行 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 命令。 此步骤下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

若要验证到群集的连接，请使用 [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令返回群集节点的列表。

```azurecli-interactive
kubectl get nodes
```

输出：

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件用于定义群集的所需状态，例如，应该运行什么容器图像。 就此示例来说，清单用于创建运行 Azure Vote 应用程序所需的所有对象。 

创建名为 `azure-vote.yml` 的文件，并将以下 YAML 复制到其中。 如果在 Azure Cloud Shell 中操作，则可使用 vi 或 Nano 来创建此文件，就像在虚拟或物理系统中操作一样。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

使用 [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 命令运行该应用程序。

```azurecli-interactive
kubectl create -f azure-vote.yml
```

输出：

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>测试应用程序

在应用程序运行以后，将创建 [Kubernetes 服务](https://kubernetes.io/docs/concepts/services-networking/service/)，向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。 

若要监视进度，请将 [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

azure-vote-front 服务的 EXTERNAL-IP 一开始显示为“挂起”。 EXTERNAL-IP 地址从“挂起”变为 IP 地址以后，请使用 `CTRL-C` 停止 kubectl 监视进程。 
  
```bash
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

现在可以浏览到外部 IP 地址来查看 Azure Vote 应用。

![浏览到 Azure Vote 的图像](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>删除群集
如果不再需要群集，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>获取代码

本快速入门使用了预先创建的容器映像来创建 Kubernetes 部署。 GitHub 上提供了相关的应用程序代码、Dockerfile 和 Kubernetes 清单文件。

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了 Kubernetes 群集，并向该群集部署了多容器应用程序。 

若要详细了解 Azure 容器服务并演练部署示例的完整代码，请继续阅读 Kubernetes 群集教程。

> [!div class="nextstepaction"]
> [管理 ACS Kubernetes 群集](./container-service-tutorial-kubernetes-prepare-app.md)
