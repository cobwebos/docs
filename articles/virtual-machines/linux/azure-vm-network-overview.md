---
title: "Azure 和 Linux VM 网络概述 | Microsoft Docs"
description: "Azure 和 Linux VM 网络的概述。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1ff4a0482d6dc6ec0eceaa89ca4b87ba1e2f89a1
ms.lasthandoff: 04/03/2017


---
# <a name="azure-and-linux-vm-network-overview"></a>Azure 和 Linux VM 网络概述
## <a name="virtual-networks"></a>虚拟网络
Azure 虚拟网络 (VNet) 是你自己的网络在云中的表示形式。 它是对专用于你的订阅的 Azure 云进行的逻辑隔离。 你可以完全控制该网络中的 IP 地址块、DNS 设置、安全策略和路由表。 还可以进一步将 VNet 细分成各个子网，并启动 Azure IaaS 虚拟机 (VM) 和/或云服务（PaaS 角色实例）。 此外，也可以使用 Azure 中提供的连接选项之一，将虚拟网络连接到本地网络。 实际上，你可以将网络扩展到 Azure，对 IP 地址块进行完全的控制，并享受企业级 Azure 带来的好处。

* [虚拟网络概述](../../virtual-network/virtual-networks-overview.md)

若要使用 Azure CLI 创建 VNet，请转到此处按照指导进行操作。

* [如何使用 Azure CLI 创建 VNet](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>网络安全组
网络安全组 (NSG) 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝虚拟网络中流向 VM 实例的网络流量。 NSG 可以与子网或该子网中的各个 VM 实例相关联。 当 NSG 与某个子网相关联时，ACL 规则适用于该子网中的所有 VM 实例。 另外，可以进一步通过将 NSG 直接关联到单个 VM 对流向该 VM 的流量进行限制。

* [什么是网络安全组 (NSG)？](../../virtual-network/virtual-networks-nsg.md)
* [如何在 Azure CLI 中创建 NSG](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>用户定义路由
当在 Azure 中将虚拟机 (VM) 添加到虚拟网络 (VNet) 时，将会注意到 VM 能够自动通过网络进行相互通信。 你不需要指定网关，即使这些 VM 位于不同子网中。 存在从 Azure 到你自己的数据中心的混合连接时，这同样适用于从 VM 到公共 Internet 甚至到本地网络的通信。

* [什么是用户定义的路由和 IP 转发？](../../virtual-network/virtual-networks-udr-overview.md)
* [在 Azure CLI 中创建 UDR](../../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>将 FQDN 关联到 Linux VM
在 Azure 门户中使用 Resource Manager 部署模型创建虚拟机 (VM) 时，该门户会为虚拟机自动创建一个公共 IP 资源。 可以使用此 IP 地址远程访问 VM。 虽然默认情况下门户不创建完全限定域名（简称 FQDN），但可以在创建 VM 后添加一个完全限定域名。

* [在 Azure 门户中创建完全限定的域名](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="network-interfaces"></a>网络接口
网络接口 (NIC) 是虚拟机 (VM) 与基础软件网络之间互相连接的桥梁。 本文解释什么是网络接口，以及在 Azure Resource Manager 部署模型中如何使用它。

* [虚拟网络接口](../../virtual-network/virtual-network-network-interface.md)

## <a name="virtual-nics-and-dns-labeling"></a>虚拟 NIC 和 DNS 标签
如果你有一个需要持久保留的服务器，但该服务器负载过重，经常拆卸和部署，你想要在 NIC 上使用 DNS 标签保留 VNET 上的名称。  使用以下演练，你将使用静态 IP 设置永久命名的 NIC。

* [使用 Azure CLI 创建完整的 Linux 环境](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>虚拟网络网关
虚拟网络网关用于在 Azure 虚拟网络和本地位置之间以及 Azure 中的虚拟网络之间（VNet 到 VNet）发送网络流量。 配置 VPN 网关时，必须创建并配置虚拟网络网关和虚拟网络网关连接。

* [关于 VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>内部负载均衡
Azure 负载均衡器是位于第 4 层 (TCP, UDP) 的负载均衡器。 该负载均衡器可以在云服务或负载均衡器集的虚拟机中运行状况良好的服务实例之间分配传入流量，从而提供高可用性。 Azure 负载均衡器还可以在多个端口和/或多个 IP 地址上显示这些服务。

* [使用 Azure CLI 创建内部负载均衡器](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)


