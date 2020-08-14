---
title: 在 Azure Kubernetes 服务 (AKS) 中自定义用户定义路由 (UDR)
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中自定义出口路由
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: 2ffe9d525e92fa2154889cea43f681a0f31a18ab
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214218"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>使用用户定义的路由自定义群集出口

可以根据具体的方案自定义 AKS 群集的出口。 默认情况下，AKS 将预配一个可设置并用于出口的标准 SKU 负载均衡器。 但是，如果禁用了公共 IP 或者出口需要额外的跃点，则默认设置可能不能满足所有方案的要求。

本文介绍了如何自定义群集的出口路由以支持自定义网络方案，例如，禁用公共 IP 并要求群集位于网络虚拟设备 (NVA) 后面。

## <a name="prerequisites"></a>先决条件
* Azure CLI 2.0.81 或更高版本
* API `2020-01-01` 或更高版本


## <a name="limitations"></a>限制
* OutboundType 只能在创建群集时定义，以后无法对其进行更新。
* 设置 `outboundType` 需要将 AKS 群集的 `vm-set-type` 指定为 `VirtualMachineScaleSets`，将 `load-balancer-sku` 指定为 `Standard`。
* 将 `outboundType` 设置为 `UDR` 值需要用户定义的路由以及群集的有效出站连接。
* 如果将 `outboundType` 设置为 `UDR` 的值，则意味着路由到负载均衡器的流入量源 IP 可能与群集的流出量目标地址不匹配。

## <a name="overview-of-outbound-types-in-aks"></a>AKS 中的出站类型概述

可以使用类型为“负载均衡器”或“用户定义的路由”的唯一 `outboundType` 来自定义 AKS 群集。

> [!IMPORTANT]
> 出站类型仅影响群集的出口流量。 有关详细信息，请参阅[设置入口控制器](ingress-basic.md)。

> [!NOTE]
> 可以将自己的[路由表][byo-route-table]与 UDR 和 kubenet 网络一起使用。 确保群集标识（服务主体或托管标识）具有对自定义路由表的“参与者”权限。

### <a name="outbound-type-of-loadbalancer"></a>loadBalancer 的出站类型

如果设置了 `loadBalancer`，AKS 将自动完成以下配置。 对于经过 AKS 分配的公共 IP 的传出流量，将使用负载均衡器。 出站类型 `loadBalancer` 支持 `loadBalancer` 类型的 Kubernetes 服务，此类型的服务应收到由 AKS 资源提供程序创建的负载均衡器传出的流量。

以下配置由 AKS 完成。
   * 为群集出口预配一个公共 IP 地址。
   * 将该公共 IP 地址分配给负载均衡器资源。
   * 为群集中的代理节点设置负载均衡器的后端池。

下面是默认情况下在 AKS 群集中部署的网络拓扑，该拓扑使用类型为 `loadBalancer` 的 `outboundType`。

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>userDefinedRouting 的出站类型

> [!NOTE]
> 使用出站类型是一种高级网络方案，需要正确配置网络。

如果设置了 `userDefinedRouting`，则 AKS 不会自动配置出口路径。 必须由你完成出口设置。

必须将 AKS 群集部署到具有以前配置的子网的现有虚拟网络，因为在不使用标准负载均衡器 (SLB) 体系结构时，必须建立显式出口。 因此，此体系结构需要向防火墙、网关、代理等设备显式发送出口流量，或需要允许网络地址转换 (NAT) 由分配给标准负载均衡器或设备的公共 IP 完成。

AKS 资源提供程序将部署一个标准负载均衡器 (SLB)。 不会为负载均衡器配置任何规则，且[在实施规则之前，负载均衡器不会产生费用](https://azure.microsoft.com/pricing/details/load-balancer/)。 AKS 不会自动为 SLB 前端预配公共 IP 地址，也不会自动配置负载均衡器后端池。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>部署出站类型为 UDR 且具有 Azure 防火墙的群集

若要演示如何应用出站类型为用户定义的路由的群集，可以通过其自己子网上的 Azure 防火墙的虚拟网络中配置一个群集。 请参阅[使用防火墙限制出口流量示例](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)上的相应示例。

> [!IMPORTANT]
> UDR 的出站类型要求路由表中有 0.0.0.0/0 的路由和 NVA（网络虚拟设备）的下一个跃点目标。
> 路由表已具有默认的 0.0.0.0/0 到 Internet 的路由，但没有连接 SNAT 的公共 IP，光是添加此路由不会为你提供出口。 AKS 将验证你没有创建指向 Internet 的 0.0.0.0/0 路由，而是创建了指向 NVA 或网关等的路由。
> 
> 使用 UDR 的出站类型时，不会创建负载平衡器公共 IP 地址，除非已配置了类型为 *loadbalancer* 的服务。

## <a name="next-steps"></a>后续步骤

参阅 [Azure 网络 UDR 概述](../virtual-network/virtual-networks-udr-overview.md)。

参阅[如何创建、更改或删除路由表](../virtual-network/manage-route-table.md)。

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
