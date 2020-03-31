---
title: 专用链接 - MySQL 的 Azure 数据库
description: 了解专用链接如何适用于 MySQL 的 Azure 数据库。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371284"
---
# <a name="private-link-for-azure-database-for-mysql"></a>MySQL Azure 数据库的专用链接

使用专用链接可以通过专用终结点连接到 Azure 中的各种 PaaS 服务。 Azure 专用链接实质上将 Azure 服务引入专用虚拟网络 （VNet）。 可以使用专用 IP 地址访问 PaaS 资源，就像 VNet 中的任何其他资源一样。

有关支持专用链接功能的 PaaS 服务的列表，请查看专用链接[文档](https://docs.microsoft.com/azure/private-link/index)。 专用终结点是特定 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 和子网中的专用 IP 地址。

> [!NOTE]
> 此功能在所有 Azure 区域都可用，其中 MySQL 的 Azure 数据库支持通用和内存优化定价层。

## <a name="data-exfiltration-prevention"></a>数据渗透防护

MySQL Azure 数据库中的数据外过滤是授权用户（如数据库管理员）能够从一个系统中提取数据并将其移动到组织外部的另一个位置或系统时。 例如，该用户将数据移到第三方拥有的存储帐户。

请考虑一个方案，即用户在 Azure 虚拟机 （VM） 内运行 MySQL 工作台，该虚拟机正在连接到美国西部预配的 MySQL 服务器的 Azure 数据库。 下面的示例演示如何使用网络访问控件限制 MySQL Azure 数据库上的公共终结点的访问。

* 通过设置 *"允许 Azure 服务关闭"，* 通过公共终结点禁用 MySQL Azure 数据库的所有 Azure 服务流量。 确保不允许通过[防火墙规则](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)或[虚拟网络服务终结点](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)访问服务器。

* 仅允许使用 VM 的专用 IP 地址访问 MySQL 的 Azure 数据库。 有关详细信息，请参阅有关[服务终结点](concepts-data-access-and-security-vnet.md)和 [VNet 防火墙规则](howto-manage-vnet-using-portal.md)的文章。

* 在 Azure VM 上，按如下所示使用网络安全组 (NSG) 和服务标记缩小传出连接的范围

    * 指定 NSG 规则以允许*服务标记和 SQL 的流量。WestUs* - 只允许连接到美国西部 MySQL 的 Azure 数据库
    * 指定 NSG 规则（具有更高的优先级）以拒绝*服务标记和 SQL*的流量 - 拒绝与所有区域中 MySQL 的 Azure 数据库的连接</br></br>

在此设置结束时，Azure VM 只能连接到美国西部区域 MySQL 的 Azure 数据库。 但是，连接不仅限于 MySQL 的单个 Azure 数据库。 VM 仍然可以连接到美国西部区域 MySQL 的任何 Azure 数据库，包括不属于订阅的数据库。 尽管我们在上述场景中已将数据渗透范围缩小到了特定的区域，但我们并未完全消除这种渗透。</br>

使用专用链接，您现在可以设置网络访问控件（如 NSG）以限制对专用终结点的访问。 然后，将单个 Azure PaaS 资源映射到特定的专用终结点。 恶意内部人员只能访问映射的 PaaS 资源（例如 MySQL 的 Azure 数据库），而无法访问其他资源。

## <a name="on-premises-connectivity-over-private-peering"></a>通过专用对等互连建立本地连接

当您从本地计算机连接到公共终结点时，需要使用服务器级防火墙规则将 IP 地址添加到基于 IP 的防火墙中。 尽管此模型非常适合用于允许对开发或测试工作负荷的单个计算机进行访问，但在生产环境中却难以管理。

使用专用链路，您可以使用[快速路由](https://azure.microsoft.com/services/expressroute/)（ER）、专用对等互连或[VPN 隧道](https://docs.microsoft.com/azure/vpn-gateway/)对专用终结点启用跨本地访问。 他们随后可以通过公共终结点禁用所有访问，而不使用基于 IP 的防火墙。

## <a name="configure-private-link-for-azure-database-for-mysql"></a>为 MySQL 配置 Azure 数据库的专用链接

### <a name="creation-process"></a>创建过程

启用专用链接需要专用终结点。 这可以使用以下操作指南来完成。

* [Azure 门户](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>审批过程
网络管理员创建专用终结点 （PE） 后，MySQL 管理员可以管理 MySQL 的专用终结点连接 （PEC） 到 Azure 数据库。 网络管理员和 DBA 之间的这种职责分离有助于管理 MySQL 连接的 Azure 数据库。 

* 导航到 Azure 门户中 MySQL 服务器资源的 Azure 数据库。 
    * 在左侧窗格中选择专用终结点连接
    * 显示所有专用终结点连接 （PEC） 的列表
    * 创建的相应专用终结点 （PE）

![选择专用终结点门户](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* 在列表中选择单个 PEC。

![选择待审批的专用终结点](media/concepts-data-access-and-security-private-link/select-private-link.png)

* MySQL 服务器管理员可以选择批准或拒绝 PEC，并可以选择添加简短的文本响应。

![选择专用终结点消息](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* 批准或拒绝后，列表将反映相应的状态以及响应文本

![选择私有终结点最终状态](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>MySQL Azure 数据库专用链接的用例

客户端可以从同一 VNet、同一区域中的对等 VNet 或通过跨区域的 VNet 到 VNet 连接连接到专用终结点。 此外，客户端可以使用 ExpressRoute、专用对等互连或 VPN 隧道从本地进行连接。 以下简化示意图显示了常见用例。

![选择专用终结点概述](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>从对等互连虚拟网络 (VNet) 中的 Azure VM 进行连接
配置[VNet 对等互连](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell)，以便从对等 VNet 中的 Azure VM 建立 MySQL Azure 数据库的连接。

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>从 VNet 到 VNet 环境中的 Azure VM 进行连接
配置[VNet 到 VNet VPN 网关连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)，以便从其他区域或订阅中的 Azure VM 建立 MySQL 的 Azure 数据库连接。

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>通过 VPN 从本地环境进行连接
要建立从本地环境到 MySQL Azure 数据库的连接，请选择并实现以下选项之一：

* [点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [站点到站点 VPN 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [快速路由电路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>专用链接与防火墙规则相结合

当您将专用链接与防火墙规则结合使用时，可能会出现以下情况和结果：

* 如果不配置任何防火墙规则，则默认情况下，任何流量都无法访问 MySQL 的 Azure 数据库。

* 如果配置公共流量或服务终结点并创建专用终结点，则不同类型的传入流量将由相应类型的防火墙规则授权。

* 如果不配置任何公共流量或服务终结点，并且创建专用终结点，则 MySQL 的 Azure 数据库只能通过专用终结点访问。 如果不配置公共流量或服务终结点，则在所有已批准的专用终结点被拒绝或删除后，任何流量都无法访问 MySQL 的 Azure 数据库。

## <a name="deny-public-access-for-azure-database-for-mysql"></a>拒绝 MySQL Azure 数据库的公共访问

如果只想依赖专用终结点来访问其 Azure 数据库的 MySQL，则可以通过在数据库服务器上设置 **"拒绝公共网络访问**"配置来禁用设置所有公共终结点（即[防火墙规则](concepts-firewall-rules.md)和[VNet 服务终结点](concepts-data-access-and-security-vnet.md)）。 

当此设置设置为 *"是"* 时，只允许通过专用终结点的连接到 MySQL 的 Azure 数据库。 当此设置设置为 NO*时*，客户端可以基于防火墙或 VNet 服务终结点设置连接到 MySQL 的 Azure 数据库。 此外，设置专用网络访问的值后，无法添加和/或更新现有防火墙和 VNet 服务终结点规则。

> [!Note]
> 此功能在所有 Azure 区域都可用，其中 Azure 数据库适用于 PostgreSQL - 单个服务器支持通用和内存优化定价层。
>
> 此设置对 MySQL Azure 数据库的 SSL 和 TLS 配置没有任何影响。

要了解如何从 Azure 门户为 MySQL 设置 Azure 数据库**的拒绝公用网络访问**，请参阅[如何配置拒绝公用网络访问](howto-deny-public-network-access.md)。

## <a name="next-steps"></a>后续步骤

要了解有关 MySQL 安全功能的 Azure 数据库的详细信息，请参阅以下文章：

* 要为 MySQL 配置 Azure 数据库的防火墙，请参阅[防火墙支持](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)。

* 要了解如何为 MySQL 的 Azure 数据库配置虚拟网络服务终结点，请参阅[配置来自虚拟网络的访问](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)。

* 有关 MySQL 连接的 Azure 数据库概述，请参阅[MySQL 连接体系结构的 Azure 数据库](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture)
