---
title: Azure 中的专用 IP 地址
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的专用 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 13688b73c0cd73e13e407f1b75beb8dda61a754f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84172252"
---
# <a name="private-ip-addresses"></a>专用 IP 地址
使用专用 IP 可在 Azure 中的资源之间进行通信。 

资源可以是：
* Azure 服务，例如：
    * 虚拟机网络接口
    * 内部负载均衡器 (ILB)
    * 应用程序网关
* （[虚拟网络](virtual-networks-overview.md)中）。
* 使用 VPN 网关或 ExpressRoute 线路的本地网络。

使用专用 IP，无需使用公共 IP 地址即可与这些资源通信。

## <a name="allocation-method"></a>分配方法

Azure 从资源所在的虚拟网络子网的地址范围中为资源分配专用 IP 地址。

Azure 保留每个子网地址范围中的前四个地址。 不能将这些地址分配给资源。 例如，如果子网的地址范围是 10.0.0.0/16，则地址 10.0.0.0-10.0.0.3 和 10.0.255.255 不可用。 子网的地址范围内的 IP 地址一次只能分配给一个资源。 

提供专用 IP 地址有两种方法：

- **动态**：Azure 会分配子网的地址范围内下一个未分配或未保留的可用 IP 地址。 例如，如果地址 10.0.0.4-10.0.0.9 已分配给其他资源，Azure 会将 10.0.0.10 分配给新资源。 

    动态方法是默认的分配方法。 分配后，如果网络接口为以下情况，则会释放动态 IP 地址：
    
    * Deleted
    * 重新分配到同一虚拟网络中的其他子网。
    * 分配方法更改为静态，并指定另一个 IP 地址。 
    
    默认情况下，当分配方法从动态更改为静态时，Azure 会将以前动态分配的地址作为静态地址分配。

- **静态**：选择并分配子网的地址范围内任何未分配或未保留的 IP 地址。 

    例如，子网的地址范围是 10.0.0.0/16，地址 10.0.0.4-10.0.0.9 分配给其他资源。 可以分配 10.0.0.10 - 10.0.255.254 之间的任何地址。 只有在删除网络接口之后，静态地址才会释放。 
    
    更改分配方法后，Azure 会将静态 IP 分配为动态 IP。 即使该地址不是子网中的下一个可用地址，也会重新分配。 将网络接口分配给其他子网时，地址会发生更改。
    
    若要将网络接口分配给其他子网，可将分配方法从静态更改为动态。 将网络接口分配给其他子网，然后将分配方法更改回静态。 分配新子网地址范围中的 IP 地址。
    
## <a name="virtual-machines"></a>虚拟机

将一个或多个专用 IP 地址分配给一个或多个网络接口。 将网络接口分配给 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机。 可将每个专用 IP 地址的分配方法指定为动态或静态。

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>内部 DNS 主机名解析（针对虚拟机）

默认情况下，使用 [Azure 托管的 DNS 服务器](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)配置 Azure 虚拟机。 可以显式配置自定义 DNS 服务器。 这些 DNS 服务器为同一个虚拟网络内的虚拟机提供内部名称解析。

将向 Azure 托管的 DNS 服务器添加主机名到虚拟机的专用 IP 地址的映射。 

当 VM 具有以下内容时，主机名将映射到主网络接口的主 IP：

* 多个网络接口
* 多个 IP 地址
* 推送、请求和匿名

配置了 Azure 托管 DNS 的 VM 会解析同一虚拟网络中的主机名。 使用自定义 DNS 服务器解析连接的虚拟网络中的 VM 的主机名。

## <a name="internal-load-balancers-ilb--application-gateways"></a>内部负载均衡器 (ILB) 和应用程序网关

可以将专用 IP 地址分配到以下服务的前端配置：

* [Azure 内部负载均衡器](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure 应用程序网关](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

此专用 IP 地址将用作内部终结点。 内部终结点仅供其虚拟网络和连接到它的远程网络中的资源访问。 可以分配动态或静态 IP。

## <a name="at-a-glance"></a>概览
下表显示了可通过其将专用 IP 与资源关联的属性。 

还显示可以使用的可能的分配方法：

* 动态
* 静态

| 顶级资源 | IP 地址关联 | 动态 | 静态 |
| --- | --- | --- | --- |
| 虚拟机 |Linux |是 |是 |
| 负载均衡器 |前端配置 |是 |是 |
| 应用程序网关 |前端配置 |是 |是 |

## <a name="limits"></a>限制
可在 Azure 中的完整[网络限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)中找到对 IP 寻址的限制。 这些限制根据区域和订阅设置。 [联系支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，根据业务需要将默认限制提高到最大限制。

## <a name="next-steps"></a>后续步骤
了解 [Azure 中的公共 IP 地址](public-ip-addresses.md)
* [通过 Azure 门户使用静态专用 IP 地址部署 VM](virtual-networks-static-private-ip-arm-pportal.md)
