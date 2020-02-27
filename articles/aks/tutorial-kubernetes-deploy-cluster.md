---
title: Azure 上的 Kubernetes 教程 - 部署群集
description: 此 Azure Kubernetes 服务 (AKS) 教程介绍如何创建 AKS 群集并使用 kubectl 连接到 Kubernetes 主节点。
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 1e977d927a301bee4be6cf3856114692542981b4
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621208"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>教程：部署 Azure Kubernetes 服务 (AKS) 群集

Kubernetes 为容器化应用程序提供一个分布式平台。 使用 AKS 可以快速创建生产就绪的 Kubernetes 群集。 在本教程的第 3 部分（共 7 部分）中，在 AKS 中部署了 Kubernetes 群集。 学习如何：

> [!div class="checklist"]
> * 部署可对 Azure 容器注册表进行身份验证的 Kubernetes AKS 群集
> * 安装 Kubernetes CLI (kubectl)
> * 配置 kubectl，以便连接到 AKS 群集

在其他教程中，Azure 投票应用程序将部署到群集，并进行缩放和更新。

## <a name="before-you-begin"></a>开始之前

在以前的教程中，已创建容器映像并上传到 Azure 容器注册表实例。 如果尚未完成这些步骤，并且想要逐一完成，请先阅读[教程 1 - 创建容器映像][aks-tutorial-prepare-app]。

此教程需要运行 Azure CLI 2.0.75 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-a-kubernetes-cluster"></a>创建 Kubernetes 群集

AKS 群集可以使用 Kubernetes 基于角色的访问控制 (RBAC)。 可以使用这些控制根据分配给用户的角色定义资源访问权限。 权限可以组合（如果为用户分配了多个角色），可以局限于单个命名空间，也可以涵盖整个群集。 默认情况下，Azure CLI 会在你创建 AKS 群集时自动启用 RBAC。

使用 [az aks create][] 创建 AKS 群集。 以下示例在名为 *myResourceGroup* 的资源组中创建名为 *myAKSCluster* 的群集。 此资源组是在[上一教程][aks-tutorial-prepare-acr]中创建的。 为了允许 AKS 群集与其他 Azure 资源进行交互，将自动创建一个 Azure Active Directory 服务主体，因为未指定该主体。 在这里，此服务主体[被授予从上一教程中创建的 Azure 容器注册表 (ACR) 实例中拉取映像][container-registry-integration]的权限。

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

几分钟后，部署完成并返回有关 AKS 部署的 JSON 格式信息。

> [!NOTE]
> 若要确保群集能够可靠运行，应至少运行 2（两个）节点。

## <a name="install-the-kubernetes-cli"></a>安装 Kubernetes CLI

若要从本地计算机连接到 Kubernetes 群集，请使用 [kubectl][kubectl]（Kubernetes 命令行客户端）。

如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 也可使用 [az aks install-cli][] 命令在本地安装它：

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>使用 kubectl 连接到群集

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][] 命令。 以下示例获取 myResourceGroup  中名为“myAKSCluster”  的 AKS 群集的凭据：

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证与群集的连接，请运行 [kubectl get nodes][kubectl-get] 命令以返回群集节点列表：

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>后续步骤

本教程在 AKS 中部署了一个 Kubernetes 群集并将 `kubectl` 配置为连接到该群集。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 部署可对 Azure 容器注册表进行身份验证的 Kubernetes AKS 群集
> * 安装 Kubernetes CLI (kubectl)
> * 配置 kubectl，以便连接到 AKS 群集

请继续学习下一教程，了解如何将应用程序部署到群集。

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
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
