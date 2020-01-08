---
title: Azure 虚拟网络（预览版）的 IPv6 概述
titlesuffix: Azure Virtual Network
description: Azure 虚拟网络中 IPv6 终结点和数据路径的 IPv6 说明。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: a44f99b5a7f25a7350e6a63cf85c6b2287647162
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75373241"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>什么是适用于 Azure 虚拟网络的 IPv6？ （预览）

使用适用于 Azure 虚拟网络（VNet）的 IPv6，可以在 Azure 中使用 IPv6 和 IPv4 连接在虚拟网络中托管应用程序，以及与 Internet 建立连接。 由于公共 IPv4 地址耗尽，新的移动网络和物联网（IoT）通常是在 IPv6 上构建的。 即使是长期建立的 ISP 和移动网络也被转换为 IPv6。 仅适用于 IPv4 的服务在现有和新兴市场中都可以发现其自身。 双堆栈 IPv4/IPv6 连接可让 Azure 托管服务通过全球可用的双堆积服务来实现这种技术缺口，这些服务可轻松地与现有 IPv4 和这些新的 IPv6 设备和网络连接。

通过 azure 的原始 IPv6 连接，可轻松为 Azure 中托管的应用程序提供双堆栈（IPv4/IPv6） Internet 连接。 它允许为入站和出站发起连接的负载均衡 IPv6 连接提供简单的 Vm 部署。 此功能仍可用，[此处](../load-balancer/load-balancer-ipv6-overview.md)提供了详细信息。
适用于 Azure 虚拟网络的 IPv6 功能更全面，可以在 Azure 中部署完整的 IPv6 解决方案体系结构。

> [!Important]
> Azure 虚拟网络的 IPv6 目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下图描绘了 Azure 中的简单双堆栈（IPv4/IPv6）部署：

![IPv6 网络部署示意图](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>优势

适用于 Azure VNET 的 IPv6 权益：

- 有助于将 Azure 托管应用程序的覆盖范围扩展到不断增长的移动和物联网市场。
- 双堆积 IPv4/IPv6 Vm 提供最高的服务部署灵活性。 单个服务实例可以与支持 IPv4 和 IPv6 的 Internet 客户端连接。
- 基于长时间建立的稳定 Azure VM 到 Internet IPv6 连接构建。
- 默认情况下，由于仅在部署中显式请求 IPv6 连接到 Internet，因此该连接是安全的。

## <a name="capabilities"></a>功能

适用于 Azure VNet 的 IPv6 包含以下功能：

- Azure 客户可以定义自己的 IPv6 虚拟网络地址空间来满足其应用程序、客户的需求，或者无缝集成到其本地 IP 空间。
- 使用双堆栈子网的双堆栈（IPv4 和 IPv6）虚拟网络，应用程序可以连接到其虚拟网络或 Internet 中的 IPv4 和 IPv6 资源。
    > [!IMPORTANT]
    > IPv6 的子网的大小必须完全相同/64。  这可确保将来能够通过将子网路由到本地网络进行兼容，因为某些路由器只能接受/64 IPv6 路由。  
- 通过网络安全组的 IPv6 规则保护你的资源。
    - Azure 平台的分布式拒绝服务（DDoS）保护扩展到面向 Internet 的公共 IP
- 自定义在虚拟网络中通过用户定义的路由对 IPv6 通信进行路由，尤其是在利用网络虚拟设备来增强应用程序时。
- Linux 和 Windows 虚拟机都可以使用适用于 Azure VNET 的 IPv6
- [标准 IPv6 公共负载均衡器](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)支持创建可复原、可缩放的应用程序，其中包括：
    - 可选 IPv6 运行状况探测，用于确定哪些后端池实例是运行状况，从而可以接收新流。
    - 可选的出站规则，提供对出站连接的完全声明性控制，以根据特定需求缩放和优化此功能。
    - 可选的多个前端配置，这些配置允许单个负载均衡器使用多个 IPv6 公共 IP 地址-相同的前端协议和端口可在前端地址之间重复使用。
    - 使用负载均衡规则的*浮动 IP*功能，可以在后端实例上重用可选 IPv6 端口 
- [标准 IPv6 内部负载均衡器](ipv6-dual-stack-standard-internal-load-balancer-powershell.md)支持在 Azure vnet 中创建弹性多层应用程序。  
- 基本 IPv6 公共负载均衡器支持与旧版部署兼容
- [保留的 Ipv6 公共 IP 地址和地址范围](ipv6-public-ip-address-prefix.md)提供稳定、可预测的 ipv6 地址，可轻松为公司和客户允许列表 azure 托管的应用程序。
- 实例层级公共 IP 提供直接连接到单个 Vm 的 IPv6 Internet 连接。
- [将 Ipv6 添加到现有的仅 ipv4 部署](ipv6-add-to-existing-vnet-powershell.md)-此功能使你能够轻松地将 IPv6 连接添加到现有的仅适用于 ipv4 的部署，而无需重新创建部署。  在此过程中，IPv4 网络流量不受影响，因此，根据您的应用程序和操作系统，您可能能够添加 IPv6，甚至可以添加到 live 服务。    
- 允许 Internet 客户端使用其所选协议，通过 Azure DNS 对 IPv6 （AAAA）记录的支持，无缝访问双 stack 应用程序。 
- 创建双堆栈应用程序，通过 IPv6 将虚拟机规模集自动缩放到你的负载。
- [虚拟网络（VNET）对等互连](virtual-network-peering-overview.md)-在区域内和全局对等互连中，使你能够无缝连接双堆栈 vnet-对等互连网络中的 vm 上的 IPv4 和 IPv6 终结点都能够彼此通信。 你甚至可以在将部署转换为双堆栈时，通过仅包含 IPv4 的 Vnet 对等端进行双向堆栈。 
- IPv6 故障排除和诊断可用于负载平衡器指标/警报和网络观察程序功能，例如数据包捕获、NSG 流日志、连接故障排除和连接监视。   

## <a name="scope"></a>范围
Azure VNET 的 IPv6 是一项基础功能集，可让客户在 Azure 中托管双堆栈（IPv4 + IPv6）应用程序。  我们打算在一段时间内将 IPv6 支持添加到更多的 Azure 网络功能，并最终提供 Azure PaaS 服务的双堆栈版本，但在此期间，可以通过双堆栈虚拟机上的 IPv4 终结点访问所有 Azure PaaS 服务。   

## <a name="limitations"></a>限制
Azure 虚拟网络的当前 IPv6 版本有以下限制：
- 适用于 Azure 虚拟网络（预览版）的 IPv6 适用于所有全球 Azure 区域，但仅适用于全球 Azure-政府云中。
- Express Route 和 VPN 网关不能用于启用了 IPv6 的 VNET 中，可以直接使用 "UseRemoteGateway" 或对等互连。 
- Azure 平台（AKS 等）不支持容器的 IPv6 通信。  

## <a name="pricing"></a>价格

IPv6 Azure 资源和带宽按照与 IPv4 相同的费率收费。 IPv6 没有其他或不同的收费。 你可以找到有关[公共 IP 地址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[网络带宽](https://azure.microsoft.com/pricing/details/bandwidth/)或[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)定价的详细信息。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure PowerShell 部署 IPv6 双堆栈应用程序](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。
- 了解如何[使用 Azure CLI 部署 IPv6 双堆栈应用程序](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)。
- 了解如何[使用资源管理器模板部署 IPv6 双重堆栈应用程序（JSON）](ipv6-configure-standard-load-balancer-template-json.md)
