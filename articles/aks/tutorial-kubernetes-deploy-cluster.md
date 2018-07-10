---
title: Kubernetes on Azure 教程 - 部署群集
description: AKS 教程 - 部署群集
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8698f16138e9baeb9c9c1142a5d0c8937a69d1b
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341393"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>教程：部署 Azure Kubernetes 服务 (AKS) 群集

Kubernetes 为容器化应用程序提供一个分布式平台。 使用 AKS 可以快速预配生产就绪的 Kubernetes 群集。 在本教程的第 3 部分（共 7 部分）中，在 AKS 中部署了 Kubernetes 群集。 已完成的步骤包括：

> [!div class="checklist"]
> * 创建用于资源交互的服务主体
> * 部署 Kubernetes AKS 群集
> * Kubernetes CLI (kubectl) 的安装
> * Kubectl 的配置

在后续教程中，Azure 投票应用程序将部署到群集，并进行缩放和更新。

## <a name="before-you-begin"></a>开始之前

在以前的教程中，已创建容器映像并上传到 Azure 容器注册表实例。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像][aks-tutorial-prepare-app]。

## <a name="create-a-service-principal"></a>创建服务主体

若要允许 AKS 群集与其他 Azure 资源交互，请使用 Azure Active Directory 服务主体。 可以通过 Azure CLI 或门户自动创建此服务主体，也可以预先创建一个服务主体并分配其他权限。 在本教程中，你将创建一个服务主体，授予对上一教程中创建的 Azure 容器注册表 (ACR) 实例的访问权限，然后创建 AKS 群集。

使用 [az ad sp create-for-rbac][] 创建服务主体。 `--skip-assignment` 参数限制分配任何其他权限。

```azurecli
az ad sp create-for-rbac --skip-assignment
```

输出类似于以下示例：

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

记下 *appId* 和 *password*。 后续步骤会用到这些值。

## <a name="configure-acr-authentication"></a>配置 ACR 身份验证

若要访问 ACR 中存储的映像，必须授予 AKS 服务主体从 ACR 提取映像的适当权限。

首先，使用 [az acr show][] 获取 ACR 资源 ID。 将 `<acrName>` 注册表名称更新为 ACR 实例和 ACR 实例所在的资源组的名称。

```azurecli
az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv
```

若要授予 AKS 群集使用 ACR 中存储的映像的适当访问权限，请使用 [az role assignment create][] 创建角色分配。 将 `<appId`> 和 `<acrId>` 替换为在前两个步骤中收集的值。

```azurecli
az role assignment create --assignee <appId> --role Reader --scope <acrId>
```

## <a name="create-kubernetes-cluster"></a>创建 Kubernetes 群集

现在，使用 [az aks create][] 创建 AKS 群集。 以下示例在名为 *myResourceGroup* 的资源组中创建名为 *myAKSCluster* 的群集。 此资源组是在[上一教程][aks-tutorial-prepare-acr]中创建的。 提供前面在创建服务主体时指定的 `<appId>` 和 `<password>`。

```azurecli
az aks create \
    --name myAKSCluster \
    --resource-group myResourceGroup \
    --node-count 1 \
    --generate-ssh-keys \
    --service-principal <appId> \
    --client-secret <password>
```

几分钟后，部署完成并返回有关 AKS 部署的 JSON 格式信息。

## <a name="install-the-kubectl-cli"></a>安装 kubectl CLI

若要从客户端计算机连接到 Kubernetes 群集，请使用 [kubectl][kubectl]（Kubernetes 命令行客户端）。

如果使用的是 Azure Cloud Shell，则 kubectl 已安装。 也可以使用 [az aks install-cli][] 在本地安装：

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>使用 kubectl 进行连接

若要将 kubectl 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][]。 以下示例获取 *myResourceGroup* 中 AKS 群集名称 *myAKSCluster* 的凭据：

```azurecli
az aks get-credentials --name myAKSCluster --resource-group myResourceGroup
```

若要验证与群集之间的连接，请运行 [kubectl get nodes][kubectl-get] 命令。

```azurecli
kubectl get nodes
```

输出：

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-66427764-0   Ready     agent     9m        v1.9.6
```

## <a name="next-steps"></a>后续步骤

本教程在 AKS 中部署了一个 Kubernetes 群集。 已完成以下步骤：

> [!div class="checklist"]
> * 创建了用于资源交互的服务主体
> * 部署了一个 Kubernetes AKS 群集
> * 安装了 Kubernetes CLI (kubectl)
> * 配置了 kubectl

继续学习下一个教程，了解如何在群集上运行应用程序。

> [!div class="nextstepaction"]
> [在 Kubernetes 中部署应用程序][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
