---
title: 将 Azure 容器注册表与 Azure Kubernetes 服务集成
description: 了解如何将 Azure Kubernetes Service (AKS) 与 Azure 容器注册表 (ACR) 集成
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: mlearned
ms.openlocfilehash: ec017901e36a01042485e9aeca2431c8a6838ab8
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536750"
---
# <a name="preview---authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>预览-从 Azure Kubernetes 服务向 Azure 容器注册表进行身份验证

结合使用 Azure 容器注册表 (ACR) 和 Azure Kubernetes 服务 (AKS) 时，需要建立身份验证机制。 本文详细介绍这两种 Azure 服务之间的建议身份验证配置。

可以通过 Azure CLI 几个简单的命令来设置 AKS 到 ACR 集成。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

必须满足以下条件：

* **Azure 订阅**上的**所有者**或**azure 帐户管理员**角色
* 还需要 Azure CLI 版本2.0.70 或更高版本以及 aks-preview 0.4.8 扩展
* 你需要在客户端上[安装 docker](https://docs.docker.com/install/) , 并需要访问[docker 中心](https://hub.docker.com/)

## <a name="install-latest-aks-cli-preview-extension"></a>安装最新的 AKS CLI 预览扩展

需要**aks-preview 0.4.8**扩展或更高版本。

```azurecli
az extension remove --name aks-preview 
az extension add -y --name aks-preview
```

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>使用 ACR 集成创建新的 AKS 群集

你可以在最初创建 AKS 群集的过程中设置 AKS 和 ACR 集成。  若要允许 AKS 群集与 ACR 交互, 请使用 Azure Active Directory**服务主体**。 以下 CLI 命令将在指定的资源组中创建一个 ACR, 并为该服务主体配置适当的**ACRPull**角色。 如果*acr 名称*不存在, 则会自动创建的`aks<resource-group>acr`默认 acr 名称。  请为以下参数提供有效值。  括号中的参数是可选的。
```azurecli
az login
az aks create -n myAKSCluster -g myResourceGroup --enable-acr [--acr <acr-name-or-resource-id>]
```
此步骤可能需要几分钟才能完成。

## <a name="create-acr-integration-for-existing-aks-clusters"></a>为现有 AKS 群集创建 ACR 集成

通过为**acr 名称**和**acr 资源 id**提供有效值, 将 ACR 与现有的 acr 群集集成。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --enable-acr --acr <acr-resource-id>
```

## <a name="log-in-to-your-acr"></a>登录到 ACR

使用以下命令登录到 ACR。  将<acrname>参数替换为 ACR 名称。  例如, 默认值为**aks < 资源组 > acr**。

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>从 docker 中心拉取映像并推送到 ACR

从 docker 中心拉取映像, 标记映像, 并推送到 ACR。

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

## <a name="update-the-state-and-verify-pods"></a>更新状态并验证盒

执行以下步骤以验证你的部署。

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

查看 yaml 文件, 并将值替换为 ACR 登录服务器、映像和标记, 以编辑图像属性。

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