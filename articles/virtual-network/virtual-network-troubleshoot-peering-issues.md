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
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796234"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>排查虚拟网络对等互连问题

本故障排除指南提供帮助你解决大多数[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)问题的步骤。

![虚拟网络对等互连示意图](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>在两个虚拟网络之间配置虚拟网络对等互连

虚拟网络是在同一订阅中还是在不同的订阅中？

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>虚拟网络位于同一订阅中

若要为同一订阅中的虚拟网络配置虚拟网络对等互连，请使用以下文章中的方法：

* 如果虚拟网络在*同一区域*中，请参阅[创建对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)。
* 如果虚拟网络位于*不同的区域*，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 

> [!Note]
> 对于以下资源，连接不会在全局虚拟网络对等互连上工作： 
>
> * 基本内部负载均衡器（ILB） SKU 后面的虚拟机（Vm）
> * Redis 缓存（使用基本 ILB SKU）
> * 应用程序网关（使用基本 ILB SKU）
> * 虚拟机规模集（使用基本 ILB SKU）
> * Azure Service Fabric 群集（使用基本 ILB SKU）
> * SQL Server Always On （使用基本 ILB SKU）
> * PowerApps 的 Azure 应用服务环境（使用 Basic ILB SKU）
> * Azure API 管理（使用基本 ILB SKU）
> * Azure Active Directory 域服务（Azure AD DS）（使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>虚拟网络在不同的订阅或 Active Directory 租户中

若要为不同订阅或 Active Directory 租户中的虚拟网络配置虚拟网络对等互连，请参阅[Azure CLI 的不同订阅中创建对等互连](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

> [!Note]
> 若要配置网络对等互连，必须在这两个订阅中拥有“网络参与者”权限。 有关详细信息，请参阅[对等互连权限](virtual-network-manage-peering.md#permissions)。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>使用本地资源的中心辐射型拓扑配置虚拟网络对等互连

![与本地辐射建立虚拟网络对等互连的示意图](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>对于站点到站点连接或 ExpressRoute 连接

按照：[为虚拟网络对等互连配置 VPN 网关传输](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)中的步骤。

### <a name="for-point-to-site-connections"></a>对于点到站点连接

1. 按照：[为虚拟网络对等互连配置 VPN 网关传输](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)中的步骤。
2. 建立或更改虚拟网络对等互连后，下载并重新安装点到站点包，以便点到站点客户端获得到辐射虚拟网络的更新路由。

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>配置具有中心辐射型拓扑虚拟网络的虚拟网络对等互连

![虚拟网络对等互连的关系图](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>虚拟网络位于同一区域


1. 在中心虚拟网络中，配置网络虚拟设备（NVA）。
1. 在辐射虚拟网络中，具有应用了下一跃点类型 "网络虚拟设备" 的用户定义路由。

有关详细信息，请参阅[服务链接](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)。

> [!Note]
> 如果需要帮助设置 NVA，请[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

在排查 NVA 设备设置和路由问题时如需帮助，请参阅 [Azure 中的网络虚拟设备问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

### <a name="the-virtual-networks-are-in-different-regions"></a>虚拟网络位于不同的区域中

现在支持通过全局虚拟网络对等互连传输。 对于以下资源，连接无法在全局虚拟网络对等互连上工作：

* 基本 ILB SKU 后面的 VM
* Redis 缓存（使用基本 ILB SKU）
* 应用程序网关（使用基本 ILB SKU）
* 规模集（使用基本 ILB SKU）
* Service Fabric 群集（使用基本 ILB SKU）
* SQL Server Always On （使用基本 ILB SKU）
* 应用服务环境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS （使用基本 ILB SKU）

若要了解有关全球对等互连要求和限制的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>排查两个对等互连虚拟网络之间的连接问题

使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。 选择虚拟网络，选择 "对**等互连**"，然后检查 "**状态**" 字段。 状态是什么？

### <a name="the-peering-status-is-connected"></a>对等互连状态为 "已连接"

若要解决此问题：

1. 检查网络流量流：

   使用从源 VM 到目标 VM 的[连接故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)和 [IP 流验证](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)，确定是否某个 NSG 或 UDR 导致流量流受到干扰。

   如果使用的是防火墙或 NVA： 
   1. 记录 UDR 参数，以便在完成此步骤后可以还原它们。
   2. 从源 VM 子网或 NIC 中删除指向 NVA（充当下一跃点）的 UDR。 验证从源 VM 直接连接到绕过 NVA 的目标的连接。 如果此步骤不起作用，请参阅[NVA 疑难解答](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

2. 提取网络跟踪： 
   1. 在目标 VM 上启动网络跟踪。 对于 Windows，可以使用 **Netsh**。 对于 Linux，请使用 **TCPDump**。
   2. 运行从源到目标 IP 的 **TcpPing** 或 **PsPing**。

      这是 **TcpPing** 命令的一个示例：`tcping64.exe -t <destination VM address> 3389`

   3. 完成 **TcpPing** 后，停止目标上的网络跟踪。
   4. 如果从源传入的数据包抵达，则表示没有网络问题。 检查 VM 防火墙以及侦听该端口的应用程序来找出配置问题。

   > [!Note]
   > 无法通过全局虚拟网络对等互连连接到以下资源类型（虚拟网络位于不同的区域中）：
   >
   > * 基本 ILB SKU 后面的 VM
   > * Redis 缓存（使用基本 ILB SKU）
   > * 应用程序网关（使用基本 ILB SKU）
   > * 规模集（使用基本 ILB SKU）
   > * Service Fabric 群集（使用基本 ILB SKU）
   > * SQL Server Always On （使用基本 ILB SKU）
   > * 应用服务环境（使用基本 ILB SKU）
   > * API 管理（使用基本 ILB SKU）
   > * Azure AD DS （使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="the-peering-status-is-disconnected"></a>对等互连状态为 "已断开连接"

若要解决此问题，请删除这两个虚拟网络的对等互连，然后重新创建它们。

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>排查中心辐射型虚拟网络与本地资源之间的连接问题

你的网络是使用第三方 NVA 还是使用 VPN 网关？

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>我的网络使用了第三方 NVA 或 VPN 网关

若要排查影响第三方 NVA 或 VPN 网关的连接问题，请参阅以下文章：

* [NVA 故障排除](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [服务链接](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>我的网络不使用第三方 NVA 或 VPN 网关

中心虚拟网络和辐射虚拟网络是否有 VPN 网关？

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>中心虚拟网络和辐射虚拟网络都有一个 VPN 网关

不支持使用远程网关。

如果辐射虚拟网络已有 VPN 网关，则在辐射虚拟网络上不支持 "**使用远程网关**" 选项。 这是因为虚拟网络对等互连限制。

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>中心虚拟网络和辐射虚拟网络都没有 VPN 网关

对于站点到站点或 Azure ExpressRoute 连接，请检查本地的远程虚拟网络的连接问题的主要原因：

* 在包含网关的虚拟网络上，验证是否选中了 "**允许转发的流量**" 复选框。
* 在没有网关的虚拟网络上，验证是否选中了 "**使用远程网关**" 复选框。
* 让网络管理员检查你的本地设备，验证是否为所有这些设备添加了远程虚拟网络地址空间。

对于点到站点连接：

* 在包含网关的虚拟网络上，验证是否选中了 "**允许转发的流量**" 复选框。
* 在没有网关的虚拟网络上，验证是否选中了 "**使用远程网关**" 复选框。
* 下载并重新安装点到站点客户端包。 新对等互连的虚拟网络路由不会自动将路由添加到点到站点客户端。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>对同一区域中的辐射虚拟网络之间的中心辐射型网络连接问题进行故障排除

集线器网络必须包含 NVA。 在将 NVA 设置为下一个跃点的轮辐中配置 Udr，并在中心虚拟网络中启用 "**允许转发的流量**"。

有关详细信息，请参阅[服务链接](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)，并与所选的 [NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)讨论这些要求。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>对不同区域中的辐射虚拟网络之间的中心辐射型网络连接问题进行故障排除

现在支持通过全局虚拟网络对等互连传输。 对于以下资源，连接不会在全局虚拟网络对等互连上工作：

* 基本 ILB SKU 后面的 VM
* Redis 缓存（使用基本 ILB SKU）
* 应用程序网关（使用基本 ILB SKU）
* 规模集（使用基本 ILB SKU）
* Service Fabric 群集（使用基本 ILB SKU）
* SQL Server Always On （使用基本 ILB SKU）
* 应用服务环境（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure AD DS （使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连和[不同 VPN 拓扑](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>对 web 应用和辐射虚拟网络之间的中心辐射型网络连接问题进行故障排除

若要解决此问题：

1. 登录到 Azure 门户。 
1. 在 web 应用中，选择 "**网络**"，然后选择 " **VNet 集成**"。
1. 检查是否可以看到远程虚拟网络。 手动输入远程虚拟网络地址空间（"**同步网络**" 和 "**添加路由**"）。

有关详细信息，请参阅以下文章：

* [将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [关于点到站点 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>排查虚拟网络对等互连配置错误消息 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>当前租户 `<TENANT ID>` 无权访问链接的订阅

若要解决此问题，请参阅[创建对等互连-Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)。

### <a name="not-connected"></a>未连接

若要解决此问题，请删除这两个虚拟网络的对等互连，然后重新创建它们。

### <a name="failed-to-peer-a-databricks-virtual-network"></a>未能对等互连虚拟网络 Databricks

若要解决此问题，请在**Azure Databricks**下配置虚拟网络对等互连，然后使用**资源 ID**指定目标虚拟网络。 有关详细信息，请参阅[对等互连虚拟网络到远程虚拟网络的 Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)。

## <a name="next-steps"></a>后续步骤

* [排查 Azure VM 间的连接问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
