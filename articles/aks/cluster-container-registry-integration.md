---
title: 将 Azure 容器注册表与 Azure Kubernetes 服务集成
description: 了解如何将 Azure Kubernetes Service （AKS）与 Azure 容器注册表（ACR）集成
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 5d8b45137ff82db6b23b5bf31eb3e8063de343bb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191327"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 本文提供在这两个 Azure 服务之间配置身份验证的示例。

可以通过 Azure CLI 几个简单的命令来设置 AKS 到 ACR 集成。

## <a name="before-you-begin"></a>开始之前

这些示例需要：

* **Azure 订阅**上的**所有者**或**azure 帐户管理员**角色
* Azure CLI 版本2.0.73 或更高版本

若要避免需要**所有者**或**Azure 帐户管理员**角色，可以手动配置服务主体或使用现有的服务主体对 AKS 中的 ACR 进行身份验证。 有关详细信息，请参阅[使用服务主体进行 ACR 身份验证](../container-registry/container-registry-auth-service-principal.md)或[使用请求从 Kubernetes 进行身份](../container-registry/container-registry-auth-kubernetes.md)验证。

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>使用 ACR 集成创建新的 AKS 群集

你可以在最初创建 AKS 群集的过程中设置 AKS 和 ACR 集成。  若要允许 AKS 群集与 ACR 交互，请使用 Azure Active Directory**服务主体**。 使用以下 CLI 命令，可以授权订阅中的现有 ACR，并为服务主体配置适当的**ACRPull**角色。 请为以下参数提供有效值。

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```
或者，您可以使用 ACR 资源 ID 指定 ACR 名称，其格式如下：

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

此步骤可能需要几分钟才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>配置现有 AKS 群集的 ACR 集成

通过为**ACR 名称**或**acr 资源 id**提供有效值（如下所示），将现有的 ACR 与现有的 AKS 群集集成。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
或者，
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

还可以使用以下项删除 ACR 与 AKS 群集之间的集成
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```
或
```
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>使用 ACR & AKS

### <a name="import-an-image-into-your-acr"></a>将图像导入 ACR

通过运行以下内容，将映像从 docker 中心导入到 ACR：


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>将示例映像从 ACR 部署到 AKS

确保具有正确的 AKS 凭据

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

创建一个名为**nginx. yaml**的文件，其中包含以下内容：

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

接下来，在 AKS 群集中运行此部署：
```
kubectl apply -f acr-nginx.yaml
```

可以通过运行以下操作来监视部署：
```
kubectl get pods
```
应有两个正在运行的 pod。
```
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
