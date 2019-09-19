---
title: 排查虚拟网络对等互连问题
description: 帮助解决大多数虚拟网络对等互连问题的步骤。
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 68d4f55d4a382f59386e72779a5f60cfc2a65338
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091099"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>排查虚拟网络对等互连问题

此故障排除指南提供帮助你解决大多数[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)问题的步骤。

![影像](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>方案 1：配置两个虚拟网络之间的虚拟网络对等互连

虚拟网络是在同一订阅中还是在不同的订阅中？

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>连接类型1：虚拟网络在同一订阅中

若要为同一订阅中的虚拟网络配置虚拟网络对等互连，请根据需要使用以下文章中提供的方法：

* 如果虚拟网络在**同一区域**中，请按照步骤为[同一订阅中的虚拟网络创建对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)。
* 如果虚拟网络在**不同的区域**中，请按照步骤设置[全局虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  

> [!Note]
> 对于以下资源，连接不会通过全局 VNet 对等互连工作： 
>
> * 基本 ILB SKU 后面的 Vm
> * Redis 缓存（使用基本 ILB SKU）
> * 应用程序网关（使用基本 ILB SKU）
> * 规模集（使用基本 ILB SKU）
> * Service Fabric 群集（使用基本 ILB SKU）
> * SQL Always on （使用基本 ILB SKU）
> * 应用服务环境（ASE）（使用基本 ILB SKU）
> * API 管理（使用基本 ILB SKU）
> * Azure Active Directory 域服务（添加）（使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>连接类型2：虚拟网络在不同的订阅或 AD 租户中

若要为不同订阅或 Active Directory 租户中的虚拟网络配置虚拟网络对等互连，请遵循在[Azure CLI 的不同订阅中创建对等互连](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)中的步骤。

> [!Note]
> 若要配置网络对等互连，必须在两个订阅中具有**网络参与者**权限。 有关详细信息，请参阅对[等互连权限](virtual-network-manage-peering.md#permissions)。

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>方案 2：使用本地资源的中心辐射型拓扑配置虚拟网络对等互连

![影像](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>连接类型1：对于站点到站点连接或 ExpressRoute 连接

按照中的步骤操作：[为虚拟网络对等互连配置 VPN 网关传输](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。

### <a name="connection-type-2-for-point-to-site-connections"></a>连接类型2：对于点到站点连接

1. 按照中的步骤操作：[为虚拟网络对等互连配置 VPN 网关传输](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)。
2. 建立或更改虚拟网络对等互连后，必须再次下载并安装点到站点包，以便点到站点客户端获取到辐射虚拟网络的更新路由。

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>方案 3：为 Azure 虚拟网络配置具有中心辐射型拓扑的虚拟网络对等互连

![影像](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>连接类型1：虚拟网络位于同一区域

必须在中心虚拟网络中配置网络虚拟设备（NVA），并在辐射虚拟网络中应用具有下一跃点 "网络虚拟设备" 的用户定义路由。 有关详细信息，请参阅[服务链](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)。

> [!Note]
> 如果需要有关设置 NVA 的帮助，请[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

有关 NVA 设备设置和路由故障排除的帮助，请参阅[Azure 中的网络虚拟设备问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>连接类型2：虚拟网络在不同的区域中

现在支持通过全局 VNet 对等互连进行传输。 对于以下资源，连接不会在全局 VNet 对等互连上工作：

* 基本 ILB SKU 后面的 Vm
* Redis 缓存（使用基本 ILB SKU）
* 应用程序网关（使用基本 ILB SKU）
* 规模集（使用基本 ILB SKU）
* Service Fabric 群集（使用基本 ILB SKU）
* SQL Always on （使用基本 ILB SKU）
* 应用服务环境（ASE）（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure Active Directory 域服务（添加）（使用基本 ILB SKU）

若要了解有关全球对等互连要求和限制的详细信息，请参阅[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>情景 4：两个对等互连虚拟网络之间存在连接问题

使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。 选择虚拟网络，选择 "对**等互连**"，然后检查 "**状态**" 字段。 什么是状态？

### <a name="connection-type-1-the-peering-status-shows-connected"></a>连接类型1：对等互连状态显示为 "已连接"

若要解决此问题，请执行以下步骤：

1. 检查网络流量：

   使用[连接故障排除](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)和[IP 流验证](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)从源 vm 到目标 vm，以确定是否存在导致流量流干扰的 NSG 或 UDR。

   如果使用防火墙或 NVA 设备，请执行以下步骤： 
   1. 记录 UDR 参数，以便在完成此步骤后可以还原它们。
   2. 从源 VM 子网或 NIC 中删除 UDR 作为下一跃点的 NVA。 验证从源 VM 直接连接到绕过 NVA 的目标的连接。 如果此步骤可行，请参阅[NVA 疑难解答](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)。

2. 获取网络跟踪： 
   1. 在目标 VM 上启动网络跟踪。 对于 Windows，你可以使用**Netsh**。 对于 Linux，请使用**TCPDump**。
   2. 从源 IP 向目标 IP 运行**TcpPing**或**PsPing** 。

   * 下面是**TcpPing**命令的示例：`tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing**完成后，停止目标上的网络跟踪。
   4. 如果数据包从源到达，则没有网络问题。 检查 VM 防火墙和侦听该端口的应用程序以找到配置问题。

   > [!Note]
   > 不能通过全局虚拟网络对等互连（不同区域中的虚拟网络）连接到以下资源类型：
   >
   > * 基本 ILB SKU 后面的 Vm
   > * Redis 缓存（使用基本 ILB SKU）
   > * 应用程序网关（使用基本 ILB SKU）
   > * 规模集（使用基本 ILB SKU）
   > * Service Fabric 群集（使用基本 ILB SKU）
   > * SQL Always on （使用基本 ILB SKU）
   > * 应用服务环境（ASE）（使用基本 ILB SKU）
   > * API 管理（使用基本 ILB SKU）
   > * Azure Active Directory 域服务（添加）（使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)。

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>连接类型2：对等互连状态显示为 "已断开连接"

必须从 Vnet 中删除对等互连，然后重新创建它们。

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>情景 5：集线器辐射虚拟网络与本地资源之间存在连接问题

你使用的是第三方 NVA 还是 VPN 网关？

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>连接类型1：我的网络使用第三方 NVA 或 VPN 网关

若要解决影响第三方 NVA 或 VPN 网关的连接问题，请参阅以下文章：

* [NVA 疑难解答](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [服务链](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>连接类型2：我的网络不是第三方 NVA 或 VPN 网关

中心和辐射虚拟网络都有 VPN 网关吗？

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>中心和辐射虚拟网络都有一个 VPN 网关

不支持使用远程网关。

由于 VNet 对等互连的限制，如果分支 VNet 已经有一个 VPN 网关，则不支持在辐射 VNet 上**使用远程网关**。

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>中心和辐射虚拟网络没有 VPN 网关

对于站点到站点连接或 ExpressRoute 连接，请检查本地的远程虚拟网络的连接问题的主要原因。

* 验证是否在具有网关的虚拟网络上选中了 "**允许转发的通信**" 复选框。
* 验证没有网关的虚拟网络上是否已选中 "**使用远程网关**" 复选框。
* 让网络管理员检查你的本地设备，以验证它们都已添加远程虚拟网络地址空间。

对于点到站点连接：

* 验证是否在具有网关的虚拟网络上选中了 "**允许转发的通信**" 复选框。
* 验证没有网关的虚拟网络上是否已选中 "**使用远程网关**" 复选框。
* 再次下载并安装点到站点客户端包。 新对等互连的虚拟网络路由不会自动将路由添加到点到站点客户端。

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>情景 6：同一区域中的辐射虚拟网络之间存在中心辐射型网络连接问题

在中心网络中，必须有 NVA，在将 NVA 设置为下一个跃点的轮辐中配置 Udr，并在中心虚拟网络中启用 "**允许转发的流量**"。

有关详细信息，请参阅[服务链](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)，并与所选的[NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)讨论这些要求。

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>方案7：在不同区域中的辐射虚拟网络之间存在中心辐射型网络连接问题

现在支持通过全局 VNet 对等互连进行传输。 对于以下资源，连接不会通过全局 VNet 对等互连工作：

* 基本 ILB SKU 后面的 Vm
* Redis 缓存（使用基本 ILB SKU）
* 应用程序网关（使用基本 ILB SKU）
* 规模集（使用基本 ILB SKU）
* Service Fabric 群集（使用基本 ILB SKU）
* SQL Always on （使用基本 ILB SKU）
* 应用服务环境（ASE）（使用基本 ILB SKU）
* API 管理（使用基本 ILB SKU）
* Azure Active Directory 域服务（添加）（使用基本 ILB SKU）

有关详细信息，请参阅全局对等互连的[要求和约束](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)，以及[不同的 VPN 拓扑](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)。

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>方案8：Web 应用和辐射虚拟网络之间存在中心辐射型网络连接问题

若要解决此问题，请执行以下步骤：

1. 登录到 Azure 门户。 请参阅 web 应用，选择 "**网络**"，然后选择 " **VNet 集成**"。
2. 检查是否可以看到远程虚拟网络。 手动输入远程虚拟网络地址空间（"**同步网络**" 和 "**添加路由**"）。

有关详细信息，请参阅以下文章：

* [将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [关于点到站点 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>方案9：配置虚拟网络对等互连时出现错误

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>错误 1：当前租户`<TENANT ID>`无权访问链接的订阅

若要解决此问题，请按照[创建对等互连-Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)中的步骤进行操作。

### <a name="error-2-not-connected"></a>错误2：未连接

必须从两个 Vnet 中删除对等互连，然后重新创建它们。

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>错误3：未能对等互连虚拟网络 Databricks

若要解决此问题，请从 " **Azure Databricks** " 边栏选项卡配置虚拟网络对等互连，然后使用**资源 ID**指定目标虚拟网络。 有关详细信息，请参阅[对等互连虚拟网络到远程虚拟网络的 Databricks](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)。

## <a name="next-steps"></a>后续步骤

* [排查 Azure VM 间的连接问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)