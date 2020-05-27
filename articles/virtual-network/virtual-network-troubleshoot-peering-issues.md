---
title: 排查虚拟网络对等互连问题
description: 帮助解决大多数虚拟网络对等互连问题的步骤。
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9685c1739a00788a974c200ddabb8cc975696b62
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587725"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>排查虚拟网络对等互连问题

本故障排除指南提供帮助你解决大多数[虚拟网络对等互连](virtual-network-peering-overview.md)问题的步骤。

![虚拟网络对等互连示意图](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>在两个虚拟网络之间配置虚拟网络对等互连

虚拟网络是在同一订阅中还是在不同的订阅中？

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>虚拟网络位于同一订阅中

若要为同一订阅中的虚拟网络配置虚拟网络对等互连，请使用以下文章中所述的方法：

* 如果虚拟网络位于同一区域，请参阅[创建对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)。
* 如果虚拟网络位于不同的区域，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 

> [!Note]
> 对于以下资源，无法通过全局虚拟网络对等互连建立连接： 
>
> * 基本内部负载均衡器 (ILB) SKU 后面的虚拟机 (VM)
> * Redis 缓存（使用基本 ILB SKU）
> * 应用程序网关（使用基本 ILB SKU）
> * 虚拟机规模集（使用基本 ILB SKU）
> * Azure Service Fabric 群集（使用基本 ILB SKU）
> * SQL Server Always-on（使用基本 ILB SKU）
> * PowerApps 的 Azure 应用服务环境（使用基本 ILB SKU）
> * Azure API 管理（使用基本 ILB SKU）
> * Azure Active Directory 域服务 (Azure AD DS)（使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>虚拟网络位于不同的订阅或 Active Directory 租户中

若要为不同订阅或 Active Directory 租户中的虚拟网络配置虚拟网络对等互连，请参阅[使用 Azure CLI 在不同的订阅中创建对等互连](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

> [!Note]
> 若要配置网络对等互连，必须在这两个订阅中拥有“网络参与者”权限。 有关详细信息，请参阅[对等互连权限](virtual-network-manage-peering.md#permissions)。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>为使用本地资源的中心辐射型拓扑配置虚拟网络对等互连

![与本地辐射建立虚拟网络对等互连的示意图](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>对于站点到站点连接或 ExpressRoute 连接

遵循以下文章中的步骤：[针对虚拟网络对等互连配置 VPN 网关传输](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。

### <a name="for-point-to-site-connections"></a>对于点到站点连接

1. 遵循以下文章中的步骤：[针对虚拟网络对等互连配置 VPN 网关传输](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。
2. 建立或更改虚拟网络对等互连后，下载并重新安装点到站点包，使点到站点客户端能够获取指向辐射虚拟网络的更新路由。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>为中心辐射型拓扑虚拟网络配置虚拟网络对等互连

![与辐射虚拟网络建立虚拟网络对等互连的示意图](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>虚拟网络位于同一区域


1. 在中心虚拟网络中配置网络虚拟设备 (NVA)。
1. 在辐射虚拟网络中，应用下一跃点类型为“网络虚拟设备”的用户定义路由。

有关详细信息，请参阅[服务链接](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)。

> [!Note]
> 如果需要帮助设置 NVA，请[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

在排查 NVA 设备设置和路由问题时如需帮助，请参阅 [Azure 中的网络虚拟设备问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

### <a name="the-virtual-networks-are-in-different-regions"></a>虚拟网络位于不同的区域

现在支持通过全球虚拟网络对等互连进行传输。 对于以下资源，无法通过全球虚拟网络对等互连建立连接：

* 基本 ILB SKU 后面的 VM
* Redis 缓存（使用基本 ILB SKU）
* 应用程序网关（使用基本 ILB SKU）
* 规模集（使用基本 ILB SKU）
* Service Fabric 群集（使用基本 ILB SKU）
* SQL Server Always-on（使用基本 ILB SKU）
* 应用服务环境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS（使用基本 ILB SKU）

若要详细了解全球对等互连要求和约束，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>排查两个对等互连虚拟网络之间的连接问题

使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到 [Azure 门户](https://portal.azure.com/)。 选择该虚拟网络，选择“对等互连”，然后检查“状态”字段。  状态是什么？

### <a name="the-peering-status-is-connected"></a>对等互连状态为“已连接”

若要排查此问题，请执行以下操作：

1. 检查网络流量流：

   在源 VM 到目标 VM 之间进行[连接故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)和 [IP 流验证](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)，确定是否某个 NSG 或 UDR 导致流量流受到干扰。

   如果使用防火墙或 NVA，请执行以下操作： 
   1. 记录 UDR 参数，以便在完成此步骤后可以还原这些参数。
   2. 从源 VM 子网或 NIC 中删除指向 NVA（充当下一跃点）的 UDR。 验证绕过 NVA 的从源 VM 到目标的直接连接。 如果此步骤不起作用，请参阅 [NVA 故障排除](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

2. 进行网络跟踪： 
   1. 在目标 VM 上启动网络跟踪。 对于 Windows，可以使用 **Netsh**。 对于 Linux，请使用 **TCPDump**。
   2. 运行从源到目标 IP 的 **TcpPing** 或 **PsPing**。

      这是 **TcpPing** 命令的一个示例：`tcping64.exe -t <destination VM address> 3389`

   3. 完成 **TcpPing** 后，停止目标上的网络跟踪。
   4. 如果从源传入的数据包抵达目标，则表示没有网络问题。 检查 VM 防火墙以及侦听该端口的应用程序以找出配置问题。

   > [!Note]
   > 无法通过全球虚拟网络对等互连（虚拟网络位于不同的区域中）连接到以下资源类型：
   >
   > * 基本 ILB SKU 后面的 VM
   > * Redis 缓存（使用基本 ILB SKU）
   > * 应用程序网关（使用基本 ILB SKU）
   > * 规模集（使用基本 ILB SKU）
   > * Service Fabric 群集（使用基本 ILB SKU）
   > * SQL Server Always-on（使用基本 ILB SKU）
   > * 应用服务环境（使用基本 ILB SKU）
   > * API 管理（使用基本 ILB SKU）
   > * Azure AD DS（使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-peering-status-is-disconnected"></a>对等互连状态为“已断开连接”

若要解决此问题，请从两个虚拟网络中删除对等互连，然后重新创建对等互连。

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>排查中心辐射型虚拟网络与本地资源之间的连接问题

网络是否使用了第三方 NVA 或 VPN 网关？

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>我的网络使用了第三方 NVA 或 VPN 网关

若要排查影响第三方 NVA 或 VPN 网关的连接问题，请参阅以下文章：

* [NVA 故障排除](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [服务链](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>我的网络未使用第三方 NVA 或 VPN 网关

中心虚拟网络和辐射虚拟网络是否使用了 VPN 网关？

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>中心虚拟网络和辐射虚拟网络都使用了 VPN 网关

不支持使用远程网关。

如果辐射虚拟网络已有 VPN 网关，则辐射虚拟网络不支持“使用远程网关”选项。 这是因为虚拟网络对等互连存在限制。

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>中心虚拟网络和辐射虚拟网络都不使用 VPN 网关

对于站点到站点连接或 Azure ExpressRoute 连接，请检查以下主要原因，这些原因会导致从本地连接到远程虚拟网络时出现问题：

* 在具有网关的虚拟网络上，验证是否选中了“允许转发的流量”复选框。
* 在没有网关的虚拟网络上，验证是否选中了“使用远程网关”复选框。
* 让网络管理员检查你的本地设备，验证是否为所有这些设备添加了远程虚拟网络地址空间。

对于点到站点连接：

* 在具有网关的虚拟网络上，验证是否选中了“允许转发的流量”复选框。
* 在没有网关的虚拟网络上，验证是否选中了“使用远程网关”复选框。
* 下载并重新安装点到站点客户端包。 新对等互连的虚拟网络路由不会自动将路由添加到点到站点客户端。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>排查同一区域中辐射虚拟网络之间的中心辐射型网络连接问题

中心网络必须包含 NVA。 在已将 NVA 设置为下一跃点的辐射虚拟网络中配置 UDR，并在中心虚拟网络中启用“允许转发的流量”。

有关详细信息，请参阅[服务链接](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)，并与所选的 [NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)讨论这些要求。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>排查不同区域中辐射虚拟网络之间的中心辐射型网络连接问题

现在支持通过全球虚拟网络对等互连进行传输。 对于以下资源，无法通过全局虚拟网络对等互连建立连接：

* 基本 ILB SKU 后面的 VM
* Redis 缓存（使用基本 ILB SKU）
* 应用程序网关（使用基本 ILB SKU）
* 规模集（使用基本 ILB SKU）
* Service Fabric 群集（使用基本 ILB SKU）
* SQL Server Always-on（使用基本 ILB SKU）
* 应用服务环境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS（使用基本 ILB SKU）

有关详细信息，请参阅全球对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)，以及[不同的 VPN 拓扑](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>排查 Web 应用与辐射虚拟网络之间的中心辐射型网络连接问题

若要排查此问题，请执行以下操作：

1. 登录到 Azure 门户。 
1. 在 Web 应用中选择“网络”，然后选择“VNet 集成”。 
1. 检查是否可以看到远程虚拟网络。 手动输入远程虚拟网络地址空间（“同步网络”和“添加路由”）。 

有关详细信息，请参阅以下文章：

* [将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [关于点到站点 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>排查出现虚拟网络对等互连配置错误消息的问题 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>当前租户 `<TENANT ID>` 无权访问链接的订阅

若要解决此问题，请参阅[创建对等互连 - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

### <a name="not-connected"></a>未连接

若要解决此问题，请从两个虚拟网络中删除对等互连，然后重新创建对等互连。

### <a name="failed-to-peer-a-databricks-virtual-network"></a>无法对等互连 Databricks 虚拟网络

若要解决此问题，请在“Azure Databricks”下配置虚拟网络对等互连，然后使用“资源 ID”指定目标虚拟网络。  有关详细信息，请参阅[将 Databricks 虚拟网络对等互连到远程虚拟网络](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)。

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>远程虚拟网络缺少网关

如果对等互连了不同租户中的虚拟网络，然后配置了 `Use Remote Gateways`，则会出现此问题。 Azure 门户存在的一个限制是，它无法验证另一租户的虚拟网络中是否存在虚拟网络网关。

可通过两种方式解决此问题：

 * 删除对等互连，并在创建新对等互连时激活 `Use Remote Gateways` 选项。
 * 使用 PowerShell 或 CLI（而不要使用 Azure 门户）启用 `Use Remote Gateways`。

## <a name="next-steps"></a>后续步骤

* [排查 Azure VM 间的连接问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
