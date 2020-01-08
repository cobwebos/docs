---
title: Azure Kubernetes Service （AKS）中的 API 服务器授权 IP 范围
description: 了解如何使用用于访问 Azure Kubernetes Service （AKS）中的 API 服务器的 IP 地址范围保护群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/05/2019
ms.author: mlearned
ms.openlocfilehash: 5f3e6cf8c5de8d5f3de17ad0b5d4bb4c004c06df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442980"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service （AKS）中的授权 IP 地址范围保护对 API 服务器的访问

在 Kubernetes 中，API 服务器接收到在群集中执行操作的请求，如创建资源或缩放节点数。 API 服务器是与群集进行交互和管理群集的中心方法。 若要提高群集安全性并将攻击降到最低，只能从有限的一组 IP 地址范围访问 API 服务器。

本文介绍如何使用 API 服务器授权的 IP 地址范围来限制哪些 IP 地址和 CIDRs 可以访问控制平面。

> [!IMPORTANT]
> 在新群集上，仅在*标准*SKU 负载平衡器上支持 API 服务器授权的 IP 地址范围。 配置了*基本*sku 负载平衡器和 API 服务器授权 IP 地址范围的现有群集将继续工作，但无法迁移到*标准*SKU 负载平衡器。 如果升级 Kubernetes 版本或控制面，则这些现有群集也会继续工作。

## <a name="before-you-begin"></a>开始之前

API 服务器授权的 IP 范围仅适用于你创建的新 AKS 群集。 本文介绍如何使用 Azure CLI 创建 AKS 群集。

需要安装并配置 Azure CLI 版本2.0.76 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 服务器授权的 IP 范围概述

Kubernetes API 服务器是基础 Kubernetes Api 的公开方式。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。 AKS 提供具有专用 API 服务器的单租户群集主机。 默认情况下，将为 API 服务器分配一个公共 IP 地址，并且应使用基于角色的访问控制（RBAC）来控制访问权限。

若要保护对其他可公开访问的 AKS 控制平面/API 服务器的访问，可以启用并使用授权 IP 范围。 这些授权的 IP 范围仅允许已定义的 IP 地址范围与 API 服务器进行通信。 阻止了从不属于这些授权 IP 范围的 IP 地址对 API 服务器发出的请求。 继续使用 RBAC 向用户及其请求的操作授权。

有关 API 服务器和其他群集组件的详细信息，请参阅[Kubernetes core 概念 FOR AKS][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>创建启用了 API 服务器授权 IP 范围的 AKS 群集

API 服务器授权的 IP 范围仅适用于新的 AKS 群集。 使用[az aks][az-aks-create] create 创建群集并指定 *--由服务器授权的 ip 范围*参数，以提供授权 ip 地址范围的列表。 这些 IP 地址范围通常是本地网络或公共 Ip 使用的地址范围。 指定 CIDR 范围时，请从该范围中的第一个 IP 地址开始。 例如， *137.117.106.90/29*是有效的范围，但请确保指定范围内的第一个 IP 地址，例如*137.117.106.88/29*。

> [!IMPORTANT]
> 默认情况下，你的群集使用[标准 SKU 负载平衡器][standard-sku-lb]，你可以使用它来配置出站网关。 在群集创建过程中启用 API 服务器授权的 IP 范围时，默认情况下，除了指定的范围之外，还默认允许群集的公共 IP。 如果为 *-----------------------ip 范围*指定 *""* 或不指定值，则将禁用 api 服务器授权 ip

以下示例在名为*myResourceGroup*的资源组中创建名为*myAKSCluster*的单节点群集，并启用 API 服务器授权的 IP 范围。 允许的 IP 地址范围为*73.140.245.0/24*：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 应将这些范围添加到允许列表中：
> - 防火墙公共 IP 地址
> - 任何表示你要从中管理群集的网络的范围
> - 如果你使用的是 AKS 群集上的 Azure Dev Spaces，则必须[根据你所在的区域允许其他范围][dev-spaces-ranges]。

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>指定标准 SKU 负载均衡器的出站 Ip

创建 AKS 群集时，如果为群集指定出站 IP 地址或前缀，则允许这些地址或前缀。 例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

在上面的示例中，参数中提供的所有 Ip （-- *---* ------------------ *--*

或者，你可以指定 *--负载均衡器--ip 前缀*参数以允许出站负载均衡器 ip 前缀。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>仅允许标准 SKU 负载均衡器的出站公共 IP

在群集创建过程中启用 API 服务器授权的 IP 范围时，默认情况下，除了指定范围外，还默认允许用于群集的标准 SKU 负载均衡器的出站公共 IP。 若要仅允许标准 SKU 负载均衡器的出站公共 IP，请在指定 *--api-服务器授权 IP 范围*参数时使用*0.0.0.0/32* 。

在以下示例中，只允许使用标准 SKU 负载均衡器的出站公共 IP，并且只能从群集内的节点访问 API 服务器。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>更新群集的 API 服务器授权的 IP 范围

若要在现有群集上更新 API 服务器授权的 IP 范围，请使用[az aks update][az-aks-update]命令并使用--- *----* ------------------ *---* ---------------- *---* ------- *---*

以下示例在名为*myResourceGroup*的资源组中，在名为*myAKSCluster*的群集上更新 API 服务器授权的 IP 范围。 要授权的 IP 地址范围为*73.140.245.0/24*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

你还可以在指定 *--api-服务器授权 ip 范围*参数时使用*0.0.0.0/32* ，以仅允许标准 SKU 负载均衡器的公共 ip。

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
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
