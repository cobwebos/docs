---
title: 将将容器注册表与 Azure Kubernetes 服务集成
description: 了解如何将 Azure Kubernetes 服务 (AKS) 与 Azure 容器注册表 (ACR) 集成
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: bbd08e49256886a1df334cbf36e6e468bb8f3895
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286783"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 本文提供在这两个 Azure 服务之间配置身份验证的示例。

可以使用 Azure CLI 通过几个简单的命令设置 AKS 与 ACR 的集成。

## <a name="before-you-begin"></a>开始之前

这些示例需要：

* **Azure 订阅**上的**所有者**或 **Azure 帐户管理员**角色
* Azure CLI 版本2.0.73 或更高版本

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>通过 ACR 集成创建新的 AKS 群集

可以在一开始创建 AKS 群集时设置 AKS 与 ACR 的集成。  若要允许 AKS 群集与 ACR 交互，请使用 Azure Active Directory **服务主体**。 以下 CLI 命令允许你在订阅中授权现有 ACR，并为服务主体配置适当的 **ACRPull** 角色。 为下面的参数提供有效值。  括号中的参数为可选。
```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --attach-acr $MYACR

```
或者，您可以使用 ACR 资源 ID 指定 ACR 名称，该资源 ID 具有以下格式：

/subscriptions/\<subscription @ no__t-1/resourceGroups/\<resource//providers/no__t-3/注册表项/@no__t-microsoft.containerregistry @ 4name 
 
```azurecli
az aks create -n myAKSCluster -g myResourceGroup --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

此步骤可能需要几分钟才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>为现有的 AKS 群集配置 ACR 集成

通过为 **acr-name** 或 **acr-resource-id** 提供有效值，将现有 ACR 与现有 AKS 群集集成，如下所示。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```
或者，
```
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

还可以使用以下命令删除 ACR 与 AKS 群集之间的集成
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
