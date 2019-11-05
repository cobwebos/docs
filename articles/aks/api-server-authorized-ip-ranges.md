---
title: Azure Kubernetes Service （AKS）中的 API 服务器授权 IP 范围
description: 了解如何使用用于访问 Azure Kubernetes Service （AKS）中的 API 服务器的 IP 地址范围保护群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472968"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service （AKS）中的授权 IP 地址范围保护对 API 服务器的访问

在 Kubernetes 中，API 服务器接收到在群集中执行操作的请求，如创建资源或缩放节点数。 API 服务器是与群集进行交互和管理群集的中心方法。 若要提高群集安全性并将攻击降到最低，只能从有限的一组 IP 地址范围访问 API 服务器。

本文介绍如何使用 API 服务器授权的 IP 地址范围来限制哪些 IP 地址和 CIDRs 可以访问控制平面。

> [!IMPORTANT]
> 在新群集上，仅在*标准*SKU 负载平衡器上支持 API 服务器授权的 IP 地址范围。 配置了*基本*SKU 负载平衡器和 API 服务器授权 IP 地址范围的现有群集将继续按原样工作。 这些现有的群集也可以升级，它们将继续工作。

## <a name="before-you-begin"></a>开始之前

本文假设使用的是使用[kubenet][kubenet]的群集。  使用基于[Azure 容器网络接口（CNI）][cni-networking]的群集时，你将不会获得安全访问所需的路由表。  你将需要手动创建路由表。  有关详细信息，请参阅[管理路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

API 服务器授权的 IP 范围仅适用于你创建的新 AKS 群集。 本文介绍如何使用 Azure CLI 创建 AKS 群集。

需要安装并配置 Azure CLI 版本2.0.76 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

配置 API 服务器授权的 IP 范围时，以下限制适用：

* 当前无法使用 Azure Dev Spaces，因为也会阻止与 API 服务器的通信。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 服务器授权的 IP 范围概述

Kubernetes API 服务器是基础 Kubernetes Api 的公开方式。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。 AKS 提供具有专用 API 服务器的单租户群集主机。 默认情况下，将为 API 服务器分配一个公共 IP 地址，并且应使用基于角色的访问控制（RBAC）来控制访问权限。

若要保护对其他可公开访问的 AKS 控制平面/API 服务器的访问，可以启用并使用授权 IP 范围。 这些授权的 IP 范围仅允许已定义的 IP 地址范围与 API 服务器进行通信。 阻止了从不属于这些授权 IP 范围的 IP 地址对 API 服务器发出的请求。 你应继续使用 RBAC，然后对用户及其请求的操作进行授权。

有关 API 服务器和其他群集组件的详细信息，请参阅[Kubernetes core 概念 FOR AKS][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

API 服务器授权的 IP 范围仅适用于新的 AKS 群集。 在群集创建操作过程中，不能启用授权的 IP 范围。 如果尝试在群集创建过程中启用授权的 IP 范围，则群集节点在部署过程中无法访问 API 服务器，因为未在该点定义出口 IP 地址。

首先，使用[az aks create][az-aks-create]命令创建群集。 以下示例在名为*myResourceGroup*的资源组中创建名为*myAKSCluster*的单节点群集。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>更新具有授权 IP 范围的群集

默认情况下，你的群集使用[标准 SKU 负载平衡器][standard-sku-lb]，你可以使用它来配置出站网关。 使用[az network 公共 ip 列表][az-network-public-ip-list]并指定 AKS 群集的资源组，该资源组通常以*MC_* 开头。 这会显示你的群集的公共 IP，你可以允许。 使用[az aks update][az-aks-update]命令并指定 *--api-服务器授权的 ip 范围*参数以允许群集的 ip。 例如：

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

若要启用 API 服务器授权的 IP 范围，请使用[az aks update][az-aks-update]命令并指定 *--API-服务器授权的 ip 范围*参数，以提供授权 IP 地址范围的列表。 这些 IP 地址范围通常是本地网络或公共 Ip 使用的地址范围。 指定 CIDR 范围时，请从该范围中的第一个 IP 地址开始。 例如， *137.117.106.90/29*是有效的范围，但请确保指定范围内的第一个 IP 地址，例如*137.117.106.88/29*。

以下示例在名为*myResourceGroup*的资源组中名为*myAKSCluster*的群集上启用 API 服务器授权的 IP 范围。 要授权的 IP 地址范围为*172.0.0.0/16* （Pod/节点地址范围）和*168.10.0.0/18* （ServiceCidr）：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> 应将这些范围添加到允许列表中：
> - 防火墙公共 IP 地址
> - 服务 CIDR
> - 子网的地址范围，其中包含节点和盒
> - 任何表示你要从中管理群集的网络的范围

## <a name="disable-authorized-ip-ranges"></a>禁用授权的 IP 范围

若要禁用授权的 IP 范围，请使用[az aks update][az-aks-update]并指定一个空范围，以禁用 API 服务器授权的 ip 范围。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>后续步骤

本文介绍了已启用 API 服务器授权的 IP 范围。 此方法是如何运行安全 AKS 群集的一部分。

有关详细信息，请参阅[AKS 中的应用程序和群集的安全概念][concepts-security]和[AKS 中群集安全性和升级的最佳方案][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
