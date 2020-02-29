---
title: Azure AD 域服务的网络规划和连接 |Microsoft Docs
description: 了解运行 Azure Active Directory 域服务时，一些用于连接的虚拟网络设计注意事项和资源。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e00ec8448739ac30950877a2ae196aa78cde750c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917333"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Azure AD 域服务的虚拟网络设计注意事项和配置选项

随着 Azure Active Directory 域服务（AD DS）向其他应用程序和工作负荷提供身份验证和管理服务，网络连接是一个关键组件。 如果未正确配置虚拟网络资源，则应用程序和工作负荷无法与通信，并使用 Azure AD DS 提供的功能。 规划虚拟网络要求，以确保 Azure AD DS 可以根据需要为你的应用程序和工作负荷提供服务。

本文概述了 Azure 虚拟网络支持 Azure AD DS 的设计注意事项和要求。

## <a name="azure-virtual-network-design"></a>Azure 虚拟网络设计

若要提供网络连接以及允许应用程序和服务针对 Azure AD DS 进行身份验证，请使用 Azure 虚拟网络和子网。 理想情况下，Azure AD DS 应部署在其自己的虚拟网络中。 可以在同一个虚拟网络中包含一个单独的应用程序子网，用于托管管理 VM 或轻型应用程序工作负载。 对于大型或复杂的应用程序工作负荷，对等互连到 Azure AD DS 虚拟网络的独立虚拟网络通常是最恰当的设计。 如果您满足以下部分中的虚拟网络和子网的要求，其他设计选择都是有效的。

设计用于 Azure AD DS 的虚拟网络时，请注意以下事项：

* 必须将 Azure AD DS 部署到与虚拟网络相同的 Azure 区域。
    * 此时，每个 Azure AD 租户只能部署一个 Azure AD DS 托管域。 Azure AD DS 托管域部署到单个区域。 请确保在[支持 AZURE AD DS 的区域](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)中创建或选择一个虚拟网络。
* 请考虑其他 Azure 区域与托管你的应用程序工作负荷的虚拟网络的接近程度。
    * 若要最大程度地减少延迟，请将核心应用程序靠近或与 Azure AD DS 托管域的虚拟网络子网位于同一区域。 你可以使用虚拟网络对等互连或 Azure 虚拟网络之间的虚拟专用网络（VPN）连接。 下一节将讨论这些连接选项。
* 虚拟网络不能依赖于 Azure AD DS 提供的 DNS 服务。
    * Azure AD DS 提供自己的 DNS 服务。 必须将虚拟网络配置为使用这些 DNS 服务地址。 可以使用条件转发器来完成其他命名空间的名称解析。
    * 不能使用自定义 DNS 服务器设置来定向来自其他 DNS 服务器（包括 Vm）的查询。 虚拟网络中的资源必须使用 Azure AD DS 提供的 DNS 服务。

> [!IMPORTANT]
> 启用该服务后，不能将 Azure AD DS 移到其他虚拟网络。

Azure AD DS 托管域连接到 Azure 虚拟网络中的子网。 为 Azure AD DS 设计此子网，注意事项如下：

* Azure AD DS 必须部署在其自己的子网中。 请勿使用现有子网或网关子网。
* 网络安全组是在部署 Azure AD DS 托管域的过程中创建的。 此网络安全组包含正确的服务通信所需的规则。
    * 不要使用您自己的自定义规则创建或使用现有的网络安全组。
* Azure AD DS 需要 3-5 IP 地址。 请确保子网 IP 地址范围可提供此数量的地址。
    * 限制可用 IP 地址可能会阻止 Azure AD 域服务维护两个域控制器。

以下示例关系图概述了 Azure AD DS 具有其自己的子网的有效设计，提供了用于外部连接的网关子网，并且应用程序工作负载位于虚拟网络内连接的子网中：

![建议的子网设计](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>与 Azure AD DS 虚拟网络的连接

如上一节所述，只能在 Azure 中的单个虚拟网络中创建一个 Azure AD 域服务托管域，并且只能为每个 Azure AD 租户创建一个托管域。 根据此体系结构，你可能需要将托管应用程序工作负载的一个或多个虚拟网络连接到 Azure AD DS 虚拟网络。

可以使用以下方法之一来连接其他 Azure 虚拟网络中托管的应用程序工作负荷：

* 虚拟网络对等互连
* 虚拟专用网络（VPN）

### <a name="virtual-network-peering"></a>虚拟网络对等互连

虚拟网络对等互连是一种通过 Azure 主干网络在同一区域连接两个虚拟网络的机制。 全局虚拟网络对等互连可以跨 Azure 区域连接虚拟网络。 对等互连后，这两个虚拟网络允许资源（例如 Vm）使用专用 IP 地址相互通信。 使用虚拟网络对等互连，可以通过部署在其他虚拟网络中的应用程序工作负荷部署 Azure AD DS 托管域。

![使用对等互连的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-peering.png)

有关详细信息，请参阅[Azure 虚拟网络对等互连概述](../virtual-network/virtual-network-peering-overview.md)。

### <a name="virtual-private-networking-vpn"></a>虚拟专用网络（VPN）

可以通过将虚拟网络配置为本地站点位置的相同方式将虚拟网络连接到另一个虚拟网络（VNet 到 VNet）。 这两个连接都使用 VPN 网关来创建使用 IPsec/IKE 的安全隧道。 此连接模型使你能够将 Azure AD DS 部署到 Azure 虚拟网络，然后连接到本地位置或其他云。

![使用 VPN 网关的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

有关使用虚拟专用网络的详细信息，请参阅[使用 Azure 门户配置 VNet 到 VNET VPN 网关连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)。

## <a name="name-resolution-when-connecting-virtual-networks"></a>连接虚拟网络时的名称解析

连接到 Azure AD 域服务虚拟网络的虚拟网络通常具有自己的 DNS 设置。 连接虚拟网络时，它不会自动配置连接虚拟网络的名称解析，以解析 Azure AD DS 托管域提供的服务。 必须将连接虚拟网络上的名称解析配置为允许应用程序工作负荷查找 Azure AD 域服务。

你可以使用支持连接虚拟网络的 DNS 服务器上的条件 DNS 转发器或从 Azure AD 域服务虚拟网络使用相同的 DNS IP 地址来启用名称解析。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS 使用的网络资源

在部署过程中，Azure AD DS 托管域会创建一些网络资源。 这些资源是成功操作和管理 Azure AD DS 托管域所必需的，不应进行手动配置。

| Azure 资源                          | 说明 |
|:----------------------------------------|:---|
| 网络接口卡                  | Azure AD DS 将托管域托管在 Windows Server 上作为 Azure Vm 运行的两个域控制器（Dc）上。 每个 VM 都有一个连接到虚拟网络子网的虚拟网络接口。 |
| 动态标准公共 IP 地址      | Azure AD DS 使用标准 SKU 公共 IP 地址与同步和管理服务通信。 有关公共 IP 地址的详细信息，请参阅[Azure 中的 IP 地址类型和分配方法](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。 |
| Azure 标准负载均衡器            | Azure AD DS 使用标准 SKU 负载均衡器进行网络地址转换（NAT）和负载平衡（与安全 LDAP 一起使用时）。 有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md) |
| 网络地址转换（NAT）规则 | Azure AD DS 在负载平衡器上创建并使用三个 NAT 规则-一个规则用于安全 HTTP 流量，另外两个规则用于保护 PowerShell 远程处理。 |
| 负载均衡器规则                     | 在 TCP 端口636上为安全 LDAP 配置 Azure AD DS 托管域时，将在负载均衡器上创建并使用三个规则来分配流量。 |

> [!WARNING]
> 请勿删除 Azure AD DS 创建的任何网络资源。 如果删除任何网络资源，则会发生 Azure AD DS 服务中断。

## <a name="network-security-groups-and-required-ports"></a>网络安全组和所需端口

[网络安全组（NSG）](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)包含一系列规则，这些规则允许或拒绝到 Azure 虚拟网络中流量的网络流量。 当你部署 Azure AD DS 时，将创建一个网络安全组，其中包含一组规则，这些规则允许服务提供身份验证和管理功能。 此默认网络安全组与 Azure AD DS 托管域部署到的虚拟网络子网相关联。

Azure AD DS 提供身份验证和管理服务需要以下网络安全组规则。 请勿编辑或删除 Azure AD DS 托管域部署到的虚拟网络子网的这些网络安全组规则。

| 端口号 | 协议 | 源                             | 目标 | 操作 | 必选 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | 是      | 与 Azure AD 租户同步。 |
| 3389        | TCP      | CorpNetSaw                         | Any         | Allow  | 是      | 域的管理。 |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | 是      | 域的管理。 |
| 636         | TCP      | Any                                | Any         | Allow  | 否       | 仅在配置安全 LDAP （LDAPS）时启用。 |

> [!WARNING]
> 请不要手动编辑这些网络资源和配置。 将配置错误的网络安全组或用户定义的路由表与在其中部署 Azure AD DS 的子网相关联时，可能会中断 Microsoft 提供的服务和管理功能。 Azure AD 租户与 Azure AD DS 托管域之间的同步也会中断。
>
> 对于网络安全组，还存在*AllowVnetInBound*、 *AllowAzureLoadBalancerInBound*、 *DenyAllInBound*、 *AllowVnetOutBound*、 *AllowInternetOutBound*和*DenyAllOutBound*的默认规则。 请勿编辑或删除这些默认规则。
>
> Azure SLA 不适用于不正确配置的网络安全组和/或用户定义的路由表，这些表已应用，阻止 Azure AD DS 更新和管理域。

### <a name="port-443---synchronization-with-azure-ad"></a>端口 443-与 Azure AD 同步

* 用于将 Azure AD 租户与 Azure AD DS 托管域同步。
* 如果不访问此端口，则 Azure AD DS 托管域无法与 Azure AD 租户同步。 如果用户的密码更改不会同步到 Azure AD DS 托管域，用户可能无法登录。
* 默认情况下，使用**AzureActiveDirectoryDomainServices**服务标记对此端口到 IP 地址的入站访问受到限制。
* 不要限制此端口的出站访问。

### <a name="port-3389---management-using-remote-desktop"></a>端口 3389-使用远程桌面管理

* 用于与 Azure AD DS 托管域中的域控制器的远程桌面连接。
* 默认网络安全组规则使用*CorpNetSaw*服务标记进一步限制流量。
    * 此服务标记仅允许 Microsoft 企业网络中的安全访问工作站使用远程桌面访问 Azure AD DS 托管域。
    * 只允许访问业务理由，例如用于管理或故障排除方案。
* 此规则可以设置为 "*拒绝*"，并且仅在需要时设置为 "*允许*"。 大多数管理和监视任务都是使用 PowerShell 远程处理来执行的。 仅当 Microsoft 需要远程连接到托管域进行高级故障排除时，才会使用 RDP。

> [!NOTE]
> 如果尝试编辑此网络安全组规则，则无法从门户中手动选择*CorpNetSaw*服务标记。 必须使用 Azure PowerShell 或 Azure CLI 手动配置使用*CorpNetSaw*服务标记的规则。

### <a name="port-5986---management-using-powershell-remoting"></a>端口 5986-使用 PowerShell 远程处理管理

* 用于在 Azure AD DS 托管域中使用 PowerShell 远程处理执行管理任务。
* 如果不访问此端口，则无法更新、配置、备份或监视 Azure AD DS 托管域。
* 对于使用基于资源管理器的虚拟网络 Azure AD DS 托管域，你可以将此端口的入站访问限制为*AzureActiveDirectoryDomainServices*服务标记。
    * 对于使用基于经典的虚拟网络的旧式 Azure AD DS 托管域，你可以限制对此端口的入站访问以下源 IP 地址： *52.180.183.8*、 *23.101.0.70*、 *52.225.184.198*、 *52.179.126.223*、 *13.74.249.156*、 *52.187.117.83*、 *52.161.13.95*、 *104.40.156.18*和*104.40.87.209*。

    > [!NOTE]
    > 在2017中，Azure AD 域服务变为可在 Azure 资源管理器网络中托管。 自那时起，我们能够使用 Azure 资源管理器的新式功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，Azure AD DS 经典虚拟网络部署将于2023年3月1日停用。
    >
    > 有关详细信息，请参阅[官方弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>用户定义路由

默认情况下不会创建用户定义的路由，因此 Azure AD DS 可以正常工作。 如果需要使用路由表，请避免对*0.0.0.0*路由进行任何更改。 更改此路由 Azure AD 域服务中断，并将托管域置于不受支持的状态。

还必须将入站流量从各自的 Azure 服务标记中包含的 IP 地址路由到 Azure AD 域服务子网。 有关中服务标记及其关联的 IP 地址的详细信息，请参阅[AZURE IP 范围和服务标记-公有云](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

> [!CAUTION]
> 这些 Azure 数据中心 IP 范围可能会更改，恕不另行通知。 确保你拥有验证你是否具有最新 IP 地址的过程。

## <a name="next-steps"></a>后续步骤

有关 Azure AD DS 使用的某些网络资源和连接选项的详细信息，请参阅以下文章：

* [Azure 虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 网络安全组](../virtual-network/security-overview.md)
