---
title: Azure 虚拟网络 （预览版） 的 IPv6 概述
titlesuffix: Azure Virtual Network
description: IPv6 在 Azure 虚拟网络中的 IPv6 终结点和数据路径的说明。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130847"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>什么是 Azure 虚拟网络的 IPv6？ （预览版）

IPv6 的 Azure 虚拟网络 (VNET)，您可以在 Azure 中托管应用程序，使用 IPv6 和 IPv4 连接同时虚拟网络内以及 Internet。 由于的公共 IPv4 地址耗尽、 移动和物联网 (IoT) 的新网络通常是基于 IPv6。 即使长时间建立 ISP 和移动网络正在转换为 IPv6。 仅使用 IPv4 的服务可以发现自己在实际中处于劣势现有和新兴市场。 双堆栈 IPv4/IPv6 连接启用了 Azure 托管的服务将遍历全球可用的双堆栈容易地与现有的 IPv4 和这些 IPv6 的新设备和网络连接的服务使用此技术空白。

Azure 的原始 IPv6 连接可以轻松地在 Azure 中托管的应用程序提供双堆栈 (IPv4/IPv6) Internet 连接。 它允许简单的部署的 Vm 具有用于启动的入站和出站连接的负载均衡 IPv6 连接。 此功能仍然可用，提供了详细信息[此处](../load-balancer/load-balancer-ipv6-overview.md)。
Azure 虚拟网络的 IPv6 是更完整特色-启用完整的 IPv6 解决方案体系结构以在 Azure 中部署。

> [!Important]
> 为 Azure 虚拟网络的 IPv6 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下图描绘了在 Azure 中的简单双堆栈 (IPv4/IPv6) 部署：

![IPv6 网络部署关系图](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>优点

Azure 虚拟网络 IPv6 优势：

- 可帮助扩大您的 Azure 托管应用程序不断移动和物联网市场的覆盖面。
- 双堆积的 IPv4/IPv6 Vm 提供最高的服务的部署灵活性。 单个服务实例可以使用 IPv4 和 ipv6 的 Internet 客户端连接。
- 长时间建立的稳定 Azure VM-到-Internet 上生成 IPv6 连接。
- 保护默认情况下，因为它显式请求在部署中时，只建立 IPv6 连接到 Internet。

## <a name="capabilities"></a>功能

虚拟机的 IPv6 支持包括以下功能：

- Azure 客户可以定义自己的 IPv6 虚拟网络地址空间来满足其应用程序，客户的需求，或无缝集成到他们的本地 IP 空间。
- 双堆栈 （IPv4 和 IPv6） 具有的虚拟网络子网双堆栈启用应用程序连接到其虚拟网络或 Internet 中的 IPv4 和 IPv6 资源。
- 网络安全组保护 IPv6 规则的资源
- 自定义路由中虚拟网络与用户定义路由的 IPv6 流量，尤其是在利用网络虚拟设备来增强您的应用程序。
- IPv6 负载均衡器支持创建可复原、 可缩放的应用程序，包括 Azure DNS AAAA 记录对 IPv6 公共 Ip 的支持。
- 轻松地添加 IPv6 连接到现有的仅使用 IPv4 的部署与就地升级。
- 创建双堆栈与虚拟机规模集负载自动缩放的应用程序。

## <a name="limitations"></a>限制
Azure 虚拟网络的 IPv6 的预览版本具有以下限制：
- Azure 虚拟网络 （预览版） 的 IPv6 目前在所有全局 Azure 区域，但只能在全局 Azure-不在政府云中。   
- 预览版门户支持为限制为仅查看得多，但并非所有 IPv6 配置，但虚拟网络的 IPv6 IPv6 部署使用 Azure Powershell 和命令行接口 (CLI) 具有完全支持和文档，（其中的示例）。
- 网络观察程序对预览版的支持仅限于 NSG 流日志和捕获网络数据包。
- 负载平衡支持预览版最初被限制为基本负载均衡器。
- 实例级公共 Ip (直接在 VM 上的公共 Ip) 不支持处于预览状态。  
- 虚拟网络对等互连 （区域或全局） 不支持处于预览状态。 

## <a name="pricing"></a>定价

IPv6 Azure 资源和带宽收费费率与 IPv4 相同。 没有对 IPv6 无其他或不同的费用。 您可以找到有关定价的详细信息[公共 IP 地址](https://azure.microsoft.com/pricing/details/ip-addresses/)，[网络带宽](https://azure.microsoft.com/pricing/details/bandwidth/)，或[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)。

## <a name="next-steps"></a>后续步骤

- 了解如何[IPv6 双堆栈应用程序使用 Azure PowerShell 部署](virtual-network-ipv4-ipv6-dual-stack-powershell.md)。
- 了解如何[IPv6 双堆栈应用程序使用 Azure CLI 部署](virtual-network-ipv4-ipv6-dual-stack-cli.md)。
