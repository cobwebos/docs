---
title: Azure 虚拟网络 IPv6 概述
titlesuffix: Azure Virtual Network
description: 介绍 Azure 虚拟网络中的 IPv6 以及 IPv6 终结点和数据路径。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c0d4c72bd60f35088773ec6dc5df80709c5ae98c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666817"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Azure 虚拟网络 IPv6 是什么？

Azure 虚拟网络 (VNet) IPv6 可让你通过虚拟网络内部的以及与 Internet 之间的 IPv6 和 IPv4 连接，在 Azure 中托管应用程序。 由于公共 IPv4 地址即将耗尽，面向移动应用和物联网 (IoT) 的新网络通常构建在 IPv6 上的基础之上。 甚至是建立了很久的 ISP 和移动网络也正在过渡到 IPv6。 在现有市场和新兴市场中，仅使用 IPv4 的服务可能会处于真正的劣势。 双堆栈 IPv4/IPv6 连接使得 Azure 托管的服务能够通过全球可用的、随时可连接现有 IPv4 和这些新 IPv6 设备与网络的双堆栈服务来弥补这种技术缺口。

借助 Azure 的原始 IPv6 连接，可以轻松为 Azure 中托管的应用程序提供双堆栈 (IPv4/IPv6) Internet 连接。 它可以通过入站和出站发起连接的负载均衡 IPv6 连接简化 VM 的部署。 此功能仍可用，详细信息请参阅[此处](../load-balancer/load-balancer-ipv6-overview.md)。
Azure 虚拟网络 IPv6 的功能要全面得多，它可以实现在 Azure 中部署完整的 IPv6 解决方案体系结构。


下图描绘了 Azure 中的简单双堆栈 (IPv4/IPv6) 部署：

![IPv6 网络部署示意图](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>优点

Azure VNET IPv6 的优势：

- 帮助将 Azure 托管应用程序的覆盖范围延伸到不断发展的移动和物联网市场。
- 双堆积 IPv4/IPv6 VM 提供最高的服务部署灵活性。 单个服务实例可以连接支持 IPv4 和 IPv6 的 Internet 客户端。
- 基于长时间建立的稳定 Azure VM 到 Internet IPv6 连接。
- 默认情况下是安全的，因为仅当在部署中明确请求与 Internet 建立 IPv6 连接时，才建立这种连接。

## <a name="capabilities"></a>功能

Azure VNet IPv6 提供以下功能：

- Azure 客户可以根据其应用程序和客户的需求定义自己的 IPv6 虚拟网络地址空间，或者无缝集成到其本地 IP 空间。
- 使用具有双堆栈子网的双堆栈（IPv4 和 IPv6）虚拟网络，应用程序可以连接到其虚拟网络或 Internet 中的 IPv4 和 IPv6 资源。
    > [!IMPORTANT]
    > IPv6 的子网大小只能是 /64。  将来当你决定将子网路由到本地网络时，这种大小可以确保兼容性，因为某些路由器只能接受 /64 IPv6 路由。  
- 使用网络安全组的 IPv6 规则保护资源。
    - Azure 平台的分布式拒绝服务 (DDoS) 防护已扩展到面向 Internet 的公共 IP
- 使用用户定义的路由在虚拟网络中自定义 IPv6 流量的路由 - 尤其是利用网络虚拟设备增强应用程序时。
- Linux 和 Windows 虚拟机都可以使用 Azure VNET IPv6
- [标准 IPv6 公共负载均衡器](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)支持创建弹性可缩放的应用程序，包括：
    - 可选的 IPv6 运行状况探测可以确定哪些后端池实例是正常的，因而可以接收新流。
    - 可选的出站规则可以根据具体的需求，以完全声明性的方式控制出站连接，以缩放和优化此功能。
    - 可选的多种前端配置可让单个负载均衡器使用多个 IPv6 公共 IP 地址 - 可在不同的前端地址中重复使用相同的前端协议和端口。
    - 可以通过负载均衡规则的“浮动 IP”功能，在后端实例上重复使用可选的 IPv6 端口** 
    - 注意：负载均衡不执行任何协议转换（无 NAT64）。 
    - 注意：IPv6 只能对 Azure VM 上的主要网络接口 (NIC) 进行负载均衡。 
- [标准 IPv6 内部负载均衡器](ipv6-dual-stack-standard-internal-load-balancer-powershell.md)支持在 Azure VNET 内部创建弹性多层应用程序。   
- 基本 IPv6 公共负载均衡器支持与传统部署实现兼容
- [保留的 Ipv6 公共 IP 地址和地址范围](ipv6-public-ip-address-prefix.md) 提供稳定、可预测的 ipv6 地址，可轻松为公司和客户筛选 azure 托管的应用程序。
- 实例级公共 IP 提供与单个 VM 的 IPv6 Internet 直接连接。
- [将 IPv6 添加到现有的仅使用 IPv4 的部署](ipv6-add-to-existing-vnet-powershell.md) - 使用此功能可以轻松将 IPv6 连接添加到现有的仅使用 IPv4 的部署，而无需重新创建部署。  在此过程中 IPv4 网络流量不受影响，因此，根据应用程序和 OS，有时甚至可以将 IPv6 添加到实时服务。    
- 可让 Internet 客户端使用所选的协议，凭借 Azure DNS 对 IPv6 (AAAA) 记录的支持无缝访问双堆栈应用程序。 
- 创建可以通过虚拟机规模集和 IPv6 根据负载自动缩放的双堆栈应用程序。
- [虚拟网络 (VNET) 对等互连](virtual-network-peering-overview.md) - 区域内部和全球对等互连 - 可以无缝连接双堆栈 VNET - 对等互连网络中的 VM 上的 IPv4 和 IPv6 终结点能够相互通信。 将部署过渡到双堆栈时，甚至可以将双堆栈对等互连到仅使用 IPv4 的 VNET。 
- 可以使用负载均衡器指标/警报与网络观察程序功能（例如数据包捕获、NSG 流日志、连接故障排除和连接监视）实现 IPv6 故障排除与诊断。   

## <a name="scope"></a>范围
Azure VNET IPv6 是一个基础功能集，可让客户在 Azure 中托管双堆栈 (IPv4 + IPv6) 应用程序。  我们有意不断地将 IPv6 支持添加到更多的 Azure 网络功能，最终提供 Azure PaaS 服务的双堆栈版本，但在此过程中，仍可以通过双堆栈虚拟机上的 IPv4 终结点访问所有 Azure PaaS 服务。   

## <a name="limitations"></a>限制
当前的适用于 Azure 虚拟网络的 IPv6 版本存在以下限制：
- 使用所有部署方法，适用于 Azure 虚拟网络的 IPv6 已在全球所有 Azure 商业版和美国政府版区域中提供。  
- 在启用了 IPv6 的 VNET 中，ExpressRoute 网关可以用于仅 IPv4 通信。  我们的路线图展示了对 IPv6 通信的支持。   
- VPN 网关不能在启用了 IPv6 的 VNET 中使用，其可以直接进行使用，或通过“UseRemoteGateway”对等互连使用。
- Azure 平台（AKS 等）不支持容器的 IPv6 通信。  
- IPv6 只能对 Azure VM 上的主要网络接口 (NIC) 进行负载均衡。 不支持到辅助 NIC 的负载均衡 IPv6 通信。    
- 不支持仅 IPv6 虚拟机或虚拟机规模集，每个 NIC 必须至少包含一个 IPv4 IP 配置。 
- 将 IPv6 添加到现有 IPv4 部署时，无法将 IPv6 范围添加到具有现有资源导航链接的 VNET。  
- 目前，Azure 公共 DNS 支持 IPv6 的正向 DNS，但尚不支持反向 DNS。   

## <a name="pricing"></a>定价

IPv6 Azure 资源和带宽按照与 IPv4 相同的费率收费。 IPv6 不收取额外的费用或不同的费用。 可以查找有关[公共 IP 地址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[网络带宽](https://azure.microsoft.com/pricing/details/bandwidth/)或[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)的定价详细信息。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure PowerShell 部署 IPv6 双堆栈应用程序](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。
- 了解如何[使用 Azure CLI 部署 IPv6 双堆栈应用程序](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)。
- 了解如何[使用资源管理器模板 (JSON) 部署 IPv6 双堆栈应用程序](ipv6-configure-standard-load-balancer-template-json.md)
