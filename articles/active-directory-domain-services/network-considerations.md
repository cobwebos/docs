---
title: Azure AD 域服务的网络规划和连接 |微软文档
description: 在运行 Azure 活动目录域服务时，了解用于连接的一些虚拟网络设计注意事项和资源。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e610bf94dfdee4e2765e4fae4259f18a9f1036b5
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639978"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Azure AD 域服务的虚拟网络设计注意事项和配置选项

由于 Azure 活动目录域服务 （AD DS） 为其他应用程序和工作负荷提供身份验证和管理服务，网络连接是一个关键组件。 如果没有正确配置的虚拟网络资源，应用程序和工作负载就无法与 Azure AD DS 提供的功能进行通信和使用。 规划虚拟网络要求，以确保 Azure AD DS 能够根据需要为应用程序和工作负荷提供服务。

本文概述了支持 Azure AD DS 的 Azure 虚拟网络的设计注意事项和要求。

## <a name="azure-virtual-network-design"></a>Azure 虚拟网络设计

要提供网络连接并允许应用程序和服务根据 Azure AD DS 进行身份验证，请使用 Azure 虚拟网络和子网。 理想情况下，Azure AD DS 应部署到其自己的虚拟网络中。 您可以在同一虚拟网络中包括单独的应用程序子网，以承载管理 VM 或轻型应用程序工作负载。 与 Azure AD DS 虚拟网络对等的较大或复杂的应用程序工作负荷的单独虚拟网络通常是最合适的设计。 其他设计选择有效，前提是您满足以下虚拟网络和子网部分中概述的要求。

在为 Azure AD DS 设计虚拟网络时，需要考虑以下因素：

* Azure AD DS 必须部署到与虚拟网络相同的 Azure 区域中。
    * 此时，您只能部署每个 Azure AD 租户的一个 Azure AD DS 托管域。 Azure AD DS 托管域部署到单个区域。 请确保在[支持 Azure AD DS 的区域](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)中创建或选择虚拟网络。
* 请考虑其他 Azure 区域和承载应用程序工作负载的虚拟网络的邻近程度。
    * 要最小化延迟，请使核心应用程序靠近或位于 Azure AD DS 托管域的虚拟网络子网的虚拟网络子网或同一区域。 可以使用 Azure 虚拟网络之间的虚拟网络对等互连或虚拟专用网络 （VPN） 连接。 以下部分将讨论这些连接选项。
* 虚拟网络不能依赖 Azure AD DS 提供的 DNS 服务以外的 DNS 服务。
    * Azure AD DS 提供其自己的 DNS 服务。 必须将虚拟网络配置为使用这些 DNS 服务地址。 可以使用条件转发器完成其他命名空间的名称解析。
    * 不能使用自定义 DNS 服务器设置来引导来自其他 DNS 服务器（包括在 VM 上）的查询。 虚拟网络中的资源必须使用 Azure AD DS 提供的 DNS 服务。

> [!IMPORTANT]
> 启用服务后，无法将 Azure AD DS 移动到其他虚拟网络。

Azure AD DS 托管域连接到 Azure 虚拟网络中的子网。 使用以下注意事项为 Azure AD DS 设计此子网：

* Azure AD DS 必须部署在其自己的子网中。 不要使用现有子网或网关子网。
* 在部署 Azure AD DS 托管域期间创建网络安全组。 此网络安全组包含正确服务通信所需的规则。
    * 不要使用自己的自定义规则创建或使用现有的网络安全组。
* Azure AD DS 需要 3-5 个 IP 地址。 确保您的子网 IP 地址范围可以提供此数量的地址。
    * 限制可用的 IP 地址可能会阻止 Azure AD 域服务维护两个域控制器。

下面的示例图概述了一个有效的设计，其中 Azure AD DS 有自己的子网，有用于外部连接的网关子网，并且应用程序工作负载位于虚拟网络中的连接子网中：

![建议的子网设计](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>连接到 Azure AD DS 虚拟网络

如上一节所述，只能在 Azure 中的单个虚拟网络中创建 Azure AD 域服务托管域，并且每个 Azure AD 租户只能创建一个托管域。 基于此体系结构，您可能需要将承载应用程序工作负载的一个或多个虚拟网络连接到 Azure AD DS 虚拟网络。

可以使用以下方法之一连接托管在其他 Azure 虚拟网络中的应用程序工作负荷：

* 虚拟网络对等互连
* 虚拟专用网络 (VPN)

### <a name="virtual-network-peering"></a>虚拟网络对等互连

虚拟网络对等互连是一种通过 Azure 主干网络在同一区域连接两个虚拟网络的机制。 全局虚拟网络对等互连可以跨 Azure 区域连接虚拟网络。 一旦对等，两个虚拟网络允许资源（如 VM）直接使用专用 IP 地址相互通信。 使用虚拟网络对等互连，可以部署 Azure AD DS 托管域，应用程序工作负载部署在其他虚拟网络中。

![使用对等互连的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-peering.png)

有关详细信息，请参阅[Azure 虚拟网络对等概述](../virtual-network/virtual-network-peering-overview.md)。

### <a name="virtual-private-networking-vpn"></a>虚拟专用网 (VPN)

您可以将虚拟网络连接到另一个虚拟网络 （VNet 到 VNet），其方式与将虚拟网络配置为本地站点位置的方式相同。 两个连接都使用 VPN 网关使用 IPsec/IKE 创建安全隧道。 此连接模型允许您将 Azure AD DS 部署到 Azure 虚拟网络，然后连接本地位置或其他云。

![使用 VPN 网关进行虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

有关使用虚拟专用网络的详细信息，请阅读使用[Azure 门户配置 VNet 到 VNet VPN 网关连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。

## <a name="name-resolution-when-connecting-virtual-networks"></a>连接虚拟网络时的名称解析

连接到 Azure AD 域服务虚拟网络的虚拟网络通常有自己的 DNS 设置。 连接虚拟网络时，它不会自动为连接虚拟网络配置名称解析，以解决 Azure AD DS 托管域提供的服务。 必须配置连接虚拟网络上的名称解析，以使应用程序工作负荷能够找到 Azure AD 域服务。

您可以使用支持连接虚拟网络的 DNS 服务器上的条件 DNS 转发器启用名称解析，或者使用 Azure AD 域服务虚拟网络中相同的 DNS IP 地址。

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS 使用的网络资源

Azure AD DS 托管域在部署期间创建一些网络资源。 这些资源是成功操作和管理 Azure AD DS 托管域所必需的，不应手动配置。

| Azure 资源                          | 说明 |
|:----------------------------------------|:---|
| 网络接口卡                  | Azure AD DS 在 Windows 服务器上作为 Azure VM 运行的两个域控制器 （DC） 上托管托管域。 每个 VM 都有一个虚拟网络接口，可连接到虚拟网络子网。 |
| 动态标准公共 IP 地址      | Azure AD DS 使用标准的 SKU 公共 IP 地址与同步和管理服务通信。 有关公共 IP 地址的详细信息，请参阅[Azure 中的 IP 地址类型和分配方法](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。 |
| Azure 标准负载均衡器            | Azure AD DS 使用标准 SKU 负载均衡器进行网络地址转换 （NAT） 和负载平衡（与安全 LDAP 一起使用时）。 有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md) |
| 网络地址转换 （NAT） 规则 | Azure AD DS 在负载均衡器上创建并使用三个 NAT 规则 - 一个用于安全 HTTP 流量的规则，以及两个用于安全 PowerShell 远程处理的规则。 |
| 负载均衡器规则                     | 当为 TCP 端口 636 上的安全 LDAP 配置 Azure AD DS 托管域时，将创建三个规则并在负载均衡器上使用以分配流量。 |

> [!WARNING]
> 不要删除或修改 Azure AD DS 创建的任何网络资源，例如手动配置负载均衡器或规则。 如果删除或修改任何网络资源，则可能发生 Azure AD DS 服务中断。

## <a name="network-security-groups-and-required-ports"></a>网络安全组和所需端口

[网络安全组 （NSG）](../virtual-network/virtual-networks-nsg.md)包含允许或拒绝网络流量到 Azure 虚拟网络中的流量的规则列表。 部署 Azure AD DS 时将创建网络安全组，该 DS 包含一组规则，允许服务提供身份验证和管理功能。 此默认网络安全组与 Azure AD DS 托管域部署到的虚拟网络子网相关联。

Azure AD DS 需要以下网络安全组规则才能提供身份验证和管理服务。 不要编辑或删除 Azure AD DS 托管域部署的虚拟网络子网的这些网络安全组规则。

| 端口号 | 协议 | 源                             | 目标 | 操作 | 必选 | 目的 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | 是      | 与 Azure AD 租户同步。 |
| 3389        | TCP      | 公司网络锯                         | Any         | Allow  | 是      | 管理您的域。 |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | 是      | 管理您的域。 |

> [!WARNING]
> 不要手动编辑这些网络资源和配置。 将配置错误的网络安全组或用户定义的路由表与部署 Azure AD DS 的子网相关联时，可能会中断 Microsoft 的服务和管理域的能力。 Azure AD 租户和 Azure AD DS 托管域之间的同步也会中断。
>
> 如果使用安全 LDAP，则可以添加所需的 TCP 端口 636 规则，以根据需要允许外部流量。 添加此规则不会将网络安全组规则置于不受支持的状态。 有关详细信息，请参阅[锁定互联网上的安全 LDAP 访问](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)
>
> 网络安全组还存在 *"允许VnetInBound"、"**允许Azure加载平衡"、**拒绝"已绑定*"、"*允许VnetOut"、**允许互联网出界*和 *"拒绝全部出界"* 的默认规则。 不要编辑或删除这些默认规则。
>
> Azure SLA 不适用于已应用了配置不正确的网络安全组和/或用户定义的路由表阻止 Azure AD DS 更新和管理域的部署。

### <a name="port-443---synchronization-with-azure-ad"></a>端口 443 - 与 Azure AD 同步

* 用于将 Azure AD 租户与 Azure AD DS 托管域同步。
* 如果没有对此端口的访问，Azure AD DS 托管域就无法与 Azure AD 租户同步。 用户可能无法登录，因为对其密码的更改不会同步到 Azure AD DS 托管域。
* 默认情况下，使用**AzureActiveDirectoryDomainDomain 服务**标记限制对此端口的 IP 地址的入站访问。
* 不要限制从此端口的出站访问。

### <a name="port-3389---management-using-remote-desktop"></a>端口 3389 - 使用远程桌面进行管理

* 用于与 Azure AD DS 托管域中的域控制器的远程桌面连接。
* 默认网络安全组规则使用*CorpNetSaw*服务标记进一步限制流量。
    * 此服务标记只允许 Microsoft 公司网络上的安全访问工作站使用远程桌面到 Azure AD DS 托管域。
    * 仅允许出于业务理由进行访问，例如对于管理或故障排除方案。
* 此规则可以设置为 *"拒绝*"，并且仅在需要时设置为 *"允许*"。 大多数管理和监视任务都是使用 PowerShell 远程处理执行的。 RDP 仅在 Microsoft 需要远程连接到托管域进行高级故障排除的罕见情况下使用。

> [!NOTE]
> 如果尝试编辑此网络安全组规则，则无法从门户手动选择*CorpNetSaw*服务标记。 您必须使用 Azure PowerShell 或 Azure CLI 手动配置使用*CorpNetSaw*服务标记的规则。

### <a name="port-5986---management-using-powershell-remoting"></a>端口 5986 - 使用 PowerShell 远程处理进行管理

* 用于在 Azure AD DS 托管域中使用 PowerShell 远程处理执行管理任务。
* 如果没有对此端口的访问，无法更新、配置、备份或监视 Azure AD DS 托管域。
* 对于使用基于资源管理器的虚拟网络的 Azure AD DS 托管域，可以将对此端口的入站访问限制为*AzureActiveDirectoryDomain 服务*标记。
    * 对于使用基于经典虚拟网络的传统 Azure AD DS 托管域， 您可以将此端口的入站访问限制为以下源 IP 地址：52.180.183.8、23.101.0.70、52.225.184.198 *、52.179.126.223*、 *13.74.249.156*、 *52.187.117.83*、 *52.161.13.95*、 *104.40.156.18*和*104.40.87.209*. *52.180.183.8* *23.101.0.70* *52.225.184.198*

    > [!NOTE]
    > 2017 年，Azure AD 域服务可用于在 Azure 资源管理器网络中托管。 自那时以来，我们一直能够使用 Azure 资源管理器的现代功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，因此 Azure AD DS 经典虚拟网络部署将于 2023 年 3 月 1 日停用。
    >
    > 有关详细信息，请参阅[官方弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>用户定义路由

默认情况下，不会创建用户定义的路由，并且 Azure AD DS 不需要正常工作。 如果需要使用路由表，请避免对*0.0.0.0*路由进行任何更改。 对此路由的更改会中断 Azure AD 域服务，并将托管域置于不受支持的状态。

还必须将入站流量从相应 Azure 服务标记中包含的 IP 地址路由到 Azure AD 域服务子网。 有关服务标记及其关联 IP 地址的详细信息，请参阅[Azure IP 范围和服务标记 - 公共云](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。

> [!CAUTION]
> 这些 Azure 数据中心 IP 范围可以更改而不通知。 确保您有流程来验证您拥有最新的 IP 地址。

## <a name="next-steps"></a>后续步骤

有关 Azure AD DS 使用的一些网络资源和连接选项的详细信息，请参阅以下文章：

* [Azure 虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 网络安全组](../virtual-network/security-overview.md)
