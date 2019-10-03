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
ms.openlocfilehash: ab744efd205d826cb7ae2c3eda7bba28f4a9bee0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097806"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>使用 Azure 容器注册表从 Azure Kubernetes 服务进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 本文详细介绍这两种 Azure 服务之间的建议身份验证配置。

可以使用 Azure CLI 通过几个简单的命令设置 AKS 与 ACR 的集成。

## <a name="before-you-begin"></a>开始之前

必须满足以下条件：

* **Azure 订阅**上的**所有者**或 **Azure 帐户管理员**角色
* 还需要 Azure CLI 版本2.0.73 或更高版本
* 需要在客户端上[安装 Docker](https://docs.docker.com/install/)，并且需要访问 [docker 中心](https://hub.docker.com/)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>通过 ACR 集成创建新的 AKS 群集

可以在一开始创建 AKS 群集时设置 AKS 与 ACR 的集成。  若要允许 AKS 群集与 ACR 交互，请使用 Azure Active Directory **服务主体**。 使用以下 CLI 命令，可以授权订阅中的现有 ACR，并为服务主体配置适当的**ACRPull**角色。 为下面的参数提供有效值。  括号中的参数为可选。
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
**ACR 资源 ID 具有以下格式：** 

/subscriptions/< 订阅 >/resourceGroups/> </providers/Microsoft.ContainerRegistry/registries/{name} 
  
此步骤可能需要几分钟才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>配置现有 AKS 群集的 ACR 集成

通过为**ACR 名称**或**acr 资源 id**提供有效值（如下所示），将现有的 ACR 与现有的 AKS 群集集成。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

还可以使用以下项删除 ACR 与 AKS 群集之间的集成
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>登录到 ACR

使用以下命令登录到 ACR。  将 <acrname> 参数替换为你的 ACR 名称。  例如，默认值为 **aks<your-resource-group>acr**。

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>从 Docker 中心拉取一个映像并将其推送到 ACR

从 Docker 中心拉取一个映像，对该映像进行标记，然后将其推送到 ACR。

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>更新状态并验证 Pod

执行以下步骤以验证部署。

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

查看 yaml 文件，然后编辑映像属性，将值替换为你的 ACR 登录服务器、映像和标记。

```
$ cat acr-nginx.yaml

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

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
