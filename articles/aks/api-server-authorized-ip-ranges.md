---
title: Azure Kubernetes 服务 (AKS) 中的 API 服务器已授权 IP 范围
description: 了解如何使用用于访问 Azure Kubernetes 服务 (AKS) 中 API 服务器的 IP 地址范围保护群集
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 4d9030e21c3b8f31c18c26fc54dc76d5b8d84a17
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85100061"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes 服务 (AKS) 中的已授权 IP 地址范围保护对 API 服务器的访问

在 Kubernetes 中，API 服务器接收在群集中执行操作的请求，例如，创建资源或缩放节点数目。 API 服务器是与群集交互和管理群集的中心位置。 为了提高群集安全性并尽量减少遭到的攻击，只应从有限的一组 IP 地址范围访问 API 服务器。

本文介绍如何使用 API 服务器已授权 IP 地址范围来限制哪些 IP 地址和 CIDR 可以访问控制平面。

> [!IMPORTANT]
> 在新的群集上，仅支持在标准 SKU 负载均衡器中使用 API 服务器已授权 IP 地址范围。 配置了基本 SKU 负载均衡器和 API 服务器已授权 IP 地址范围的现有群集将继续按原有方式工作，但不能迁移到标准 SKU 负载均衡器 。 即使 Kubernetes 版本或控制平面升级后，这些现有群集也会继续工作。

## <a name="before-you-begin"></a>准备阶段

本文介绍如何使用 Azure CLI 创建 AKS 群集。

需要安装并配置 Azure CLI 2.0.76 或更高版本。 运行  `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 服务器已授权 IP 范围的概述

Kubernetes API 服务器用于公开基础 Kubernetes API。 此组件为管理工具（如 `kubectl` 或 Kubernetes 仪表板）提供交互。 AKS 为单租户群集主机提供专用的 API 服务器。 默认将为 API 服务器分配一个公共 IP 地址，你应使用基于角色的访问控制 (RBAC) 来控制访问。

若要保护对其他可公开访问的 AKS 控制平面/API 服务器的访问，可以启用并使用已授权 IP 范围。 这些已授权 IP 范围仅允许定义的 IP 地址范围与 API 服务器通信。 不属于这些授权 IP 范围的 IP 地址发出的对 API 服务器的请求将被阻止。 请继续使用 RBAC 来授权用户及其请求的操作。

有关 API 服务器和其他群集组件的详细信息，请参阅 [AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>创建启用 API 服务器已授权 IP 范围的 AKS 群集

API 服务器授权的 IP 范围仅适用于新的 AKS 群集，不适用于专用 AKS 群集。 使用[az aks create][az-aks-create]创建群集，并指定 *`--api-server-authorized-ip-ranges`* 参数以提供授权 IP 地址范围的列表。 这些 IP 地址范围通常是本地网络或公共 IP 使用的地址范围。 指定 CIDR 范围时，请先指定该范围内的第一个 IP 地址。 例如，*137.117.106.90/29* 是有效范围，但请确保指定该范围内的第一个 IP 地址，如 *137.117.106.88/29*。

> [!IMPORTANT]
> 群集默认使用可用于配置出站网关的[标准 SKU 负载均衡器][standard-sku-lb]。 在创建群集期间启用 API 服务器已授权 IP 范围时，除了允许指定的范围以外，默认还允许群集的公共 IP。 如果为指定 *""* 或不指定值 *`--api-server-authorized-ip-ranges`* ，则将禁用 API 服务器授权的 IP 范围。 请注意，如果使用的是 PowerShell，请使用 *`--api-server-authorized-ip-ranges=""`* （带有等号）来避免任何分析问题。

以下示例在名为 *myResourceGroup* 的资源组中，创建名为 *myAKSCluster*、已启用 API 服务器已授权 IP 范围的单节点群集。 允许的 IP 地址范围为 *73.140.245.0/24*：

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
> 应将以下范围添加到允许列表：
> - 防火墙公共 IP 地址
> - 代表你要从中管理群集的网络的任何范围
> - 如果在 AKS 群集上使用 Azure Dev Spaces，必须[根据所在的区域允许其他范围][dev-spaces-ranges]。

> 可指定的 IP 范围数的上限为 3500。 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>指定标准 SKU 负载均衡器的出站 IP

创建 AKS 群集时，如果为群集指定出站 IP 地址或前缀，则也会允许这些地址或前缀。 例如：

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

在上面的示例中，参数中提供的所有 Ip *`--load-balancer-outbound-ip-prefixes`* 都允许与参数中的 ip 一起使用 *`--api-server-authorized-ip-ranges`* 。

或者，你可以指定 *`--load-balancer-outbound-ip-prefixes`* 参数以允许出站负载均衡器 IP 前缀。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>仅允许标准 SKU 负载均衡器的出站公共 IP

在创建群集期间启用 API 服务器已授权 IP 范围时，除了允许指定的范围以外，默认还允许群集的标准 SKU 负载均衡器的出站公共 IP。 若要仅允许标准 SKU 负载均衡器的出站公共 IP，请在指定参数时使用*0.0.0.0/32* *`--api-server-authorized-ip-ranges`* 。

在以下示例中，仅允许标准 SKU 负载均衡器的出站公共 IP，你只能从群集中的节点访问 API 服务器。

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>更新群集的 API 服务器已授权 IP 范围

若要在现有群集上更新 API 服务器授权的 IP 范围，请使用[az aks update][az-aks-update]命令并使用 *`--api-server-authorized-ip-ranges`* ,--负载平衡器--* *`--load-balancer-outbound-ips`* ---------------------------------*

以下示例更新名为 *myResourceGroup* 的资源组中名为 *myAKSCluster* 的群集上的 API 服务器已授权 IP 范围。 要授权的 IP 地址范围为 *73.140.245.0/24*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

指定参数时，还可以使用*0.0.0.0/32* *`--api-server-authorized-ip-ranges`* ，只允许标准 SKU 负载均衡器的公共 IP。

## <a name="disable-authorized-ip-ranges"></a>禁用已授权 IP 范围

若要禁用 API 服务器已授权 IP 范围，请使用 [az aks update][az-aks-update] 并指定一个空范围。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>后续步骤

在本文中，你启用了 API 服务器已授权 IP 范围。 这是运行安全 AKS 群集的方法之一。

有关详细信息，请参阅 [AKS 中应用程序和群集的 安全性概念][concepts-security]以及[有关 AKS 中群集安全性和升级的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
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
