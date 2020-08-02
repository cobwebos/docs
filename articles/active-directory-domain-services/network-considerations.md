---
title: Azure AD 域服务的网络计划和连接 | Microsoft Docs
description: 了解运行 Azure Active Directory 域服务时的一些虚拟网络设计注意事项以及连接所用的资源。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: c811240beea896683f891d9513a657b0689b8824
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488646"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的虚拟网络设计注意事项和配置选项

Azure Active Directory 域服务（Azure AD DS）为其他应用程序和工作负荷提供身份验证和管理服务。 网络连接是一个重要的组件。 如果未正确配置虚拟网络资源，应用程序和工作负载将无法与 Azure AD DS 提供的功能进行通信并使用它们。 计划虚拟网络要求，确保 Azure AD DS 可以根据需要为应用程序和工作负载提供服务。

本文概述了为 Azure AD DS 提供支持的 Azure 虚拟网络设计注意事项和要求。

## <a name="azure-virtual-network-design"></a>Azure 虚拟网络设计

若要提供网络连接以及允许应用程序和服务针对 Azure AD DS 托管域进行身份验证，请使用 Azure 虚拟网络和子网。 理想情况下，托管域应部署在其自己的虚拟网络中。

可以在同一虚拟网络中加入独立的应用程序子网，用于承载管理 VM 或轻量级应用程序工作负载。 为较大型或复杂应用程序工作负载设计单独的虚拟网络，并使其与 Azure AD DS 虚拟网络对等互连，通常是最合适的设计。

如果其他设计满足以下各部分概述的对虚拟网络和子网的要求，则其同样有效。

为 Azure AD DS 设计虚拟网络时，应考虑以下注意事项：

* Azure AD DS 必须部署到虚拟网络所在的 Azure 区域。
    * 目前，每个 Azure AD 租户只能部署一个托管域。 托管域将部署到单个区域。 请确保在[支持 Azure AD DS 的区域](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)中创建或选择虚拟网络。
* 请考虑其他 Azure 区域与承载应用程序工作负载的虚拟网络的邻近程度。
    * 为最大程度地降低延迟，请使核心应用程序接近或处于托管域的虚拟网络子网所在区域。 可以在 Azure 虚拟网络之间使用虚拟网络对等互连或虚拟专用网络 (VPN) 连接。 以下部分讨论这些连接选项。
* 虚拟网络不能依赖于托管域提供的服务以外的 DNS 服务。
    * Azure AD DS 提供其自己的 DNS 服务。 必须将虚拟网络配置为使用这些 DNS 服务地址。 可以使用条件转发器完成其他命名空间的名称解析。
    * 不能使用自定义 DNS 服务器设置来引导来自其他 DNS 服务器（包括 VM 上的 DNS 服务器）的查询。 虚拟网络中的资源必须使用托管域提供的 DNS 服务。

> [!IMPORTANT]
> 启用服务后，无法将 Azure AD DS 迁移到其他虚拟网络。

托管域连接到 Azure 虚拟网络中的子网。 为 Azure AD DS 设计该子网，考虑以下注意事项：

* 托管域必须部署在其自己的子网中。 请勿使用现有子网或网关子网。
* 部署托管域期间创建了一个网络安全组。 此网络安全组包含正确进行服务通信所需的规则。
    * 请勿创建或使用拥有自定义规则的网络安全组。
* 托管域需要 3-5 IP 地址。 请确保子网 IP 地址范围可以提供这么多地址。
    * 限制可用 IP 地址可阻止托管域维护两个域控制器。

下面的示例关系图概述了一个有效的设计，其中，托管域具有其自己的子网，有一个用于外部连接的网关子网，并且应用程序工作负载位于虚拟网络内的连接的子网中：

![建议的子网设计](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>与 Azure AD DS 虚拟网络的连接

如上一节所述，只能在 Azure 中的单个虚拟网络中创建托管域，每个 Azure AD 租户只能创建一个托管域。 根据此体系结构，你可能需要将一个或多个托管你的应用程序工作负荷的虚拟网络连接到托管域的虚拟网络。

可以使用以下方法之一连接其他 Azure 虚拟网络中承载的应用程序工作负载：

* 虚拟网络对等互连
* 虚拟专用网络 (VPN)

### <a name="virtual-network-peering"></a>虚拟网络对等互连

虚拟网络对等互连是一种通过 Azure 主干网络在同一区域连接两个虚拟网络的机制。 全局虚拟网络对等互连可以跨 Azure 区域连接虚拟网络。 建立对等互连后，资源（例如 VM）即可使用专用 IP 地址通过这两个虚拟网络直接彼此通信。 使用虚拟网络对等互连，可以部署托管域，并在其他虚拟网络中部署应用程序工作负载。

![使用对等互连的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-peering.png)

有关详细信息，请参阅 [Azure 虚拟网络对等互连概述](../virtual-network/virtual-network-peering-overview.md)。

### <a name="virtual-private-networking-vpn"></a>虚拟专用网络 (VPN)

可以以将虚拟网络配置到本地站点位置的方式，将虚拟网络连接到另一个虚拟网络（VNet 到 VNet）。 这两种连接都使用 VPN 网关来创建使用 IPsec/IKE 的安全隧道。 此连接模型使你能够将托管域部署到 Azure 虚拟网络，然后连接到本地位置或其他云。

![使用 VPN 网关的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

若要详细了解如何使用虚拟专用网络连接，请参阅[使用 Azure 门户配置 VNet 到 VNet VPN 网关连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。

## <a name="name-resolution-when-connecting-virtual-networks"></a>连接虚拟网络时的名称解析

连接到托管域的虚拟网络的虚拟网络通常具有自己的 DNS 设置。 连接虚拟网络时，它不会自动配置名称解析，让连接的虚拟网络能够解析托管域提供的服务。 连接的虚拟网络上的名称解析必须配置为允许应用程序工作负载查找托管域。

你可以使用支持连接虚拟网络的 DNS 服务器上的条件 DNS 转发器或从托管域的虚拟网络使用相同的 DNS IP 地址来启用名称解析。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS 使用的网络资源

托管域在部署期间会创建一些网络资源。 这些资源是有效运行和管理托管域所必需的，不应手动配置。

| Azure 资源                          | 说明 |
|:----------------------------------------|:---|
| 网络接口卡                  | Azure AD DS 将托管域托管在 Windows Server 中作为 Azure VM 运行的两个域控制器 (DC) 上。 每个 VM 都有一个连接到虚拟网络子网的虚拟网络接口。 |
| 动态标准公共 IP 地址      | Azure AD DS 使用标准 SKU 公共 IP 地址与同步和管理服务进行通信。 有关公共 IP 地址的详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。 |
| Azure 标准负载均衡器            | Azure AD DS 使用标准 SKU 负载均衡器进行网络地址转换 (NAT) 和负载均衡（与安全 LDAP 一起使用时）。 有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md) |
| 网络地址转换 (NAT) 规则 | Azure AD DS 在负载均衡器上创建并使用三项 NAT 规则 - 一项面向安全 HTTP 流量，另外两项面向安全 PowerShell 远程处理。 |
| 负载均衡器规则                     | 在 TCP 端口 636 上为安全 LDAP 配置托管域时，将在负载均衡器上创建并使用三项规则来分配流量。 |

> [!WARNING]
> 请勿删除或修改 Azure AD DS 创建的任何网络资源，例如手动配置负载均衡器或规则。 如果删除或修改任何网络资源，可能会导致 Azure AD DS 服务中断。

## <a name="network-security-groups-and-required-ports"></a>网络安全组和必需端口

[网络安全组 (NSG)](../virtual-network/virtual-networks-nsg.md) 包含一系列规则，这些规则可以允许或拒绝网络流量在 Azure 虚拟网络中流动。 网络安全组是在部署包含一组规则的托管域时创建的，这些规则允许服务提供身份验证和管理功能。 此默认网络安全组与托管域部署到的虚拟网络子网相关联。

托管域需要以下网络安全组规则来提供身份验证和管理服务。 请勿编辑或删除托管域所部署到的虚拟网络子网中的这些网络安全组规则。

| 端口号 | 协议 | 源                             | 目标 | 操作 | 必须 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | 任意         | 允许  | 是      | 与 Azure AD 租户同步。 |
| 3389        | TCP      | CorpNetSaw                         | 任意         | 允许  | 是      | 用于管理域。 |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | 任意         | 允许  | 是      | 用于管理域。 |

创建一个要求实施这些规则的 Azure 标准负载均衡器。 此网络安全组会保护 Azure AD DS，是托管域正常运行所需的。 请勿删除此网络安全组。 如果没有此负载均衡器，则无法正常工作。

> [!WARNING]
> 请勿手动编辑这些网络资源和配置。 将配置错误的网络安全组或用户定义的路由表与在其中部署托管域的子网相关联时，可能会中断 Microsoft 提供的服务和管理域功能。 Azure AD 租户与托管域之间的同步也会中断。
>
> 如果使用安全 LDAP，可以添加所需的 TCP 端口 636 规则，以便在需要时允许外部流量。 添加此规则不会导致网络安全组规则不受支持。 有关详细信息，请参阅[通过 Internet 锁定安全 LDAP 访问](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> 网络安全组还有默认的 AllowVnetInBound、AllowAzureLoadBalancerInBound、DenyAllInBound、AllowVnetOutBound、AllowInternetOutBound 和 DenyAllOutBound 规则     。 请勿编辑或删除这些默认规则。
>
> Azure SLA 不适用于这样的部署：应用了配置不当的网络安全组和/或用户定义的路由表，使得 Azure AD DS 无法更新和管理域。

### <a name="port-443---synchronization-with-azure-ad"></a>端口 443 - 与 Azure AD 同步

* 用于将 Azure AD 租户与托管域同步。
* 如果不允许访问此端口，则托管域不会与 Azure AD 租户同步。 用户可能无法登录，因为对其密码所做的更改不会同步到托管域。
* 默认使用 AzureActiveDirectoryDomainServices 服务标记在此端口上限制对 IP 地址的入站访问。
* 请勿限制由此端口实现的出站访问。

### <a name="port-3389---management-using-remote-desktop"></a>端口 3389 - 使用远程桌面进行管理

* 用于与托管域的域控制器建立远程桌面连接。
* 默认网络安全组规则使用 CorpNetSaw 服务标记进一步限制流量。
    * 该服务标记仅允许 Microsoft 企业网络中的安全访问工作站使用远程桌面访问托管域。
    * 只能基于业务理由允许访问，例如管理方案或对其进行故障排除。
* 可将此规则设置为“拒绝”，仅在需要时才设置为“允许” 。 大多数管理和监视任务都使用 PowerShell 远程处理执行。 只有在极少数情况下，当 Microsoft 需要远程连接到你的托管域来进行高级故障排除时，才会使用 RDP。

> [!NOTE]
> 如果尝试编辑此网络安全组规则，则不能从门户中手动选择 CorpNetSaw 服务标记。 必须使用 Azure PowerShell 或 Azure CLI 手动配置使用 CorpNetSaw 服务标记的规则。

### <a name="port-5986---management-using-powershell-remoting"></a>端口 5986 - 使用 PowerShell 远程处理进行管理

* 用于通过托管域上的 PowerShell 远程处理来执行管理任务。
* 如果不允许访问此端口，则无法更新、配置、备份或监视托管域。
* 对于使用基于资源管理器的虚拟网络的托管域，可以将对此端口的入站访问限制于 AzureActiveDirectoryDomainServices 服务标记。
    * 对于使用经典虚拟网络的旧版托管域，可以将对此端口的入站访问限制为以下源 IP 地址：52.180.183.8、23.101.0.70、52.225.184.198、52.179.126.223、13.74.249.156、52.187.117.83、52.161.13.95、104.40.156.18 和 104.40.87.209        。

    > [!NOTE]
    > 在 2017 年，Azure AD 域服务变得可以在 Azure 资源管理器网络中托管。 自那时起，我们就能够使用 Azure 资源管理器的新式功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，因此 Azure AD DS 经典虚拟网络部署将于 2023 年 3 月 1 日停用。
    >
    > 有关详细信息，请查看[正式的弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>用户定义路由

默认不会创建用户定义的路由，并且 Azure AD DS 无需使用用户定义的路由即可正常运行。 如果需要使用路由表，请避免对 0.0.0.0 路由进行任何更改。 对此路由的更改会中断 Azure AD DS，并将托管域置于不受支持的状态。

还必须将入站流量从各自的 Azure 服务标记中包含的 IP 地址路由到托管域的子网。 有关服务标记及其关联的 IP 地址的详细信息，请参阅 [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

> [!CAUTION]
> 这些 Azure 数据中心 IP 范围可能会发生更改，恕不另行通知。 请务必准备好用于验证自己是否具有最新 IP 地址的流程。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure AD DS 使用的某些网络资源和连接选项，请参阅以下文章：

* [Azure 虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 网络安全组](../virtual-network/security-overview.md)
