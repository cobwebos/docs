---
title: Azure 虚拟网络 (预览版) 的 IPv6 概述
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
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 33078439e8f055d746fad9949a9b0d7651e120f7
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543820"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>什么是适用于 Azure 虚拟网络的 IPv6？ (预览)

使用适用于 Azure 虚拟网络 (VNet) 的 IPv6, 可以在 Azure 中使用 IPv6 和 IPv4 连接在虚拟网络中托管应用程序, 以及与 Internet 建立连接。 由于公共 IPv4 地址耗尽, 新的移动网络和物联网 (IoT) 通常是在 IPv6 上构建的。 即使是长期建立的 ISP 和移动网络也被转换为 IPv6。 仅适用于 IPv4 的服务在现有和新兴市场中都可以发现其自身。 双堆栈 IPv4/IPv6 连接可让 Azure 托管服务通过全球可用的双堆积服务来实现这种技术缺口, 这些服务可轻松地与现有 IPv4 和这些新的 IPv6 设备和网络连接。

通过 azure 的原始 IPv6 连接, 可轻松为 Azure 中托管的应用程序提供双堆栈 (IPv4/IPv6) Internet 连接。 它允许为入站和出站发起连接的负载均衡 IPv6 连接提供简单的 Vm 部署。 此功能仍可用,[此处](../load-balancer/load-balancer-ipv6-overview.md)提供了详细信息。
适用于 Azure 虚拟网络的 IPv6 功能更全面, 可以在 Azure 中部署完整的 IPv6 解决方案体系结构。

> [!Important]
> Azure 虚拟网络的 IPv6 目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下图描绘了 Azure 中的简单双堆栈 (IPv4/IPv6) 部署:

![IPv6 网络部署示意图](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>优点

Azure 虚拟网络 IPv6 权益:

- 有助于将 Azure 托管应用程序的覆盖范围扩展到不断增长的移动和物联网市场。
- 双堆积 IPv4/IPv6 Vm 提供最高的服务部署灵活性。 单个服务实例可以与支持 IPv4 和 IPv6 的 Internet 客户端连接。
- 基于长时间建立的稳定 Azure VM 到 Internet IPv6 连接构建。
- 默认情况下, 由于仅在部署中显式请求 IPv6 连接到 Internet, 因此该连接是安全的。

## <a name="capabilities"></a>功能

适用于 VNet 的 IPv6 包含以下功能:

- Azure 客户可以定义自己的 IPv6 虚拟网络地址空间来满足其应用程序、客户的需求, 或者无缝集成到其本地 IP 空间。
- 使用双堆栈子网的双堆栈 (IPv4 和 IPv6) 虚拟网络, 应用程序可以连接到其虚拟网络或 Internet 中的 IPv4 和 IPv6 资源。
    > [!IMPORTANT]
    > IPv6 的子网的大小必须完全相同/64。  如果决定启用子网到本地网络的路由, 这可确保兼容性, 因为某些路由器只能接受/64 IPv6 路由。  
- 通过网络安全组的 IPv6 规则保护你的资源。
- 自定义在虚拟网络中通过用户定义的路由对 IPv6 通信进行路由, 尤其是在利用网络虚拟设备来增强应用程序时。
- 允许 Internet 客户端使用其所选协议, 通过 Azure DNS 对 IPv6 (AAAA) 记录的支持, 无缝访问双 stack 应用程序。 
- 针对可复原、可缩放应用程序创建的标准 IPv6 公共负载均衡器支持，其中包括：
    - 可选 IPv6 运行状况探测, 用于确定哪些后端池实例是运行状况, 从而可以接收新流。 .  
    - 可选的出站规则, 提供对出站连接的完全声明性控制, 以根据特定需求缩放和优化此功能。
    - 可选的多个前端配置, 这些配置允许单个负载均衡器使用多个 IPv6 公共 IP 地址-相同的前端协议和端口可在前端地址之间重复使用。
- 实例层级公共 IP 向单个 Vm 直接提供 IPv6 Internet 连接。
- 轻松地将 IPv6 连接添加到就地就地升级的现有仅适用于 IPv4 的部署。
- 创建可根据虚拟机规模集自动缩放到负载的双堆栈应用程序。
- 预览版的门户支持包括交互式创建/编辑/删除双堆栈 (IPv4 + IPv6) 虚拟网络和子网、IPv6 网络安全组规则、IPv6 用户定义路由和 IPv6 公共 Ip。  

## <a name="limitations"></a>限制
Azure 虚拟网络的 IPv6 预览版本具有以下限制:
- 适用于 Azure 虚拟网络 (预览版) 的 IPv6 适用于所有全球 Azure 区域, 但仅适用于全球 Azure, 而非政府云。
- 门户支持标准负载均衡器组件是仅查看。  但是, 可以使用 Azure Powershell 和命令行接口 (CLI) 为标准负载均衡器部署提供完全支持和文档 (包括示例)。   
- 预览版的网络观察程序支持仅限于 NSG 流日志和网络数据包捕获。
- 预览不支持虚拟网络对等互连 (突破或全局)。
- 使用标准 IPv6 外部负载平衡器时, 以下限制适用: 
  - 出站规则可以引用多个前端公共 Ip, 但不得引用 IPv6 公共前缀。 IP 公共前缀仅支持 IPv4 前缀。
  - IPv6 负载均衡规则**不**能使用*浮动 IP*功能。 仅在 IPv4 中支持对后端实例使用端口。
- Azure 公共 IP 地址前缀功能不支持保留面向 Internet 的 IPv6 地址块。

## <a name="pricing"></a>定价

IPv6 Azure 资源和带宽按照与 IPv4 相同的费率收费。 IPv6 没有其他或不同的收费。 你可以找到有关[公共 IP 地址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[网络带宽](https://azure.microsoft.com/pricing/details/bandwidth/)或[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)定价的详细信息。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 Azure PowerShell 部署 IPv6 双堆栈应用程序](virtual-network-ipv4-ipv6-dual-stack-powershell.md)。
- 了解如何[使用 Azure CLI 部署 IPv6 双堆栈应用程序](virtual-network-ipv4-ipv6-dual-stack-cli.md)。
