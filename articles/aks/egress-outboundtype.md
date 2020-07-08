---
title: 在 Azure Kubernetes 服务 (AKS) 中自定义用户定义路由 (UDR)
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中定义自定义流出量路由
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: d98261629f9e1612966bf74084ae0e0fa70d3605
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829240"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>使用用户定义的路由自定义群集传出

可以自定义 AKS 群集的流出量，以适应特定方案。 默认情况下，AKS 将预配标准 SKU 负载均衡器，以便对出口进行设置和使用。 但是，如果不允许公共 IP，或者流出量需要其他跃点，则默认设置可能无法满足所有方案的要求。

本文介绍如何自定义群集的流出量路由以支持自定义网络方案，例如不允许使用公共 IP 并要求群集位于网络虚拟设备 (NVA) 后面的那些方案。

## <a name="prerequisites"></a>先决条件
* Azure CLI 2.0.81 或更高版本
* API `2020-01-01` 版或更高版本


## <a name="limitations"></a>限制
* OutboundType 只能在群集创建时定义，以后不能更新。
* 设置 `outboundType` 要求 AKS 群集具有 `vm-set-type` 的 `VirtualMachineScaleSets` 和 `Standard` 的 `load-balancer-sku`。
* 将 `outboundType` 设置为 `UDR` 的值需要用户定义的路由，该路由具有群集的有效出站连接。
* 如果将 `outboundType` 设置为 `UDR` 的值，则意味着路由到负载均衡器的流入量源 IP 可能与群集的流出量目标地址不匹配。

## <a name="overview-of-outbound-types-in-aks"></a>AKS 中的出站类型概述

可以使用唯一的 `outboundType` 类型的负载均衡器或用户定义的路由来自定义 AKS 群集。

> [!IMPORTANT]
> 出站类型仅影响群集的出口流量。 有关详细信息，请参阅[设置入口控制器](ingress-basic.md)。

> [!NOTE]
> 可以将自己的[路由表][byo-route-table]与 UDR 和 kubenet 网络一起使用。 请确保群集标识（服务主体或托管标识）具有自定义路由表的参与者权限。

### <a name="outbound-type-of-loadbalancer"></a>LoadBalancer 出站类型

如果 `loadBalancer` 设置了，AKS 将自动完成以下配置。 负载均衡器用于通过 AKS 分配的公共 IP 的流出量。 `loadBalancer` 出站类型支持 `loadBalancer` 类型的 Kubernetes 服务，该服务期望使用 AKS 资源提供程序创建的负载均衡器的流出量。

以下配置由 AKS 完成。
   * 针对群集流出量预配公共 IP 地址。
   * 将公共 IP 地址分配给负载均衡器资源。
   * 为群集中的代理节点设置了负载均衡器的后端池。

下面是默认情况下在 AKS 群集中部署的网络拓扑，该网络拓扑使用的 `outboundType` 为 `loadBalancer`。

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>UserDefinedRouting 出站类型

> [!NOTE]
> 使用出站类型是一种高级网络方案，需要正确的网络配置。

如果 `userDefinedRouting` 设置了，则 AKS 不会自动配置出口路径。 必须由你完成出口设置。

必须将 AKS 群集部署到具有以前配置的子网的现有虚拟网络，因为使用标准负载均衡器（SLB）体系结构时，必须建立显式传出。 因此，此体系结构需要将传出流量显式发送到设备（例如防火墙、网关、代理），或允许通过分配给标准负载平衡器或设备的公共 IP 来完成网络地址转换（NAT）。

AKS 资源提供程序将部署标准负载均衡器 (SLB)。 负载均衡器未使用任何规则进行配置，在[下一条规则之前，不会产生任何费用](https://azure.microsoft.com/pricing/details/load-balancer/)。 AKS**不**会自动为 SLB 前端预配公共 IP 地址，也不会自动配置负载均衡器后端池。

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>使用 UDR 和 Azure 防火墙出站类型部署群集

若要说明使用用户定义的路由的出站类型的应用程序，可以在其自己的子网上使用 Azure 防火墙的虚拟网络上配置群集。 请参阅[通过 Azure 防火墙限制传出流量示例](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)中的此示例。

> [!IMPORTANT]
> UDR 的出站类型需要路由表中 NVA （网络虚拟设备）的 0.0.0.0/0 和下一跃点目标的路由。
> 路由表已将默认 0.0.0.0/0 设置为 Internet，无需向 SNAT 发送公共 IP，只需添加此路由就不会为你提供出口。 AKS 将验证你是否不会创建指向 Internet，而是改为 NVA 或网关等的 0.0.0.0/0 路由。


## <a name="next-steps"></a>后续步骤

请参阅 [Azure 网络 UDR 概述](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。

请参阅[如何创建、更改或删除路由表](https://docs.microsoft.com/azure/virtual-network/manage-route-table)。

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet