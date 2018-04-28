---
title: 关于 Azure 点到站点路由 | Microsoft Docs
description: 本文介绍点到站点 VPN 路由的工作原理。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: anzaman
ms.openlocfilehash: d25709fb4abb1b8a35596c3dc246f7419a99419b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="about-point-to-site-vpn-routing"></a>关于点到站点 VPN 路由

本文介绍 Azure 点到站点 VPN 路由的工作原理。 P2S VPN 路由行为依赖于客户端 OS、用于 VPN 连接的协议，以及虚拟网络 (VNet) 相互之间的连接方式。

Azure 当前支持两种远程访问协议：IKEv2 和 SSTP。 IKEv2 可用于许多客户端操作系统，包括 Windows、Linux、、MacOS、Android 和 iOS。 SSTP 仅可用于 Windows。 如果更改网络拓扑并且具有 VPN 客户端，必须再次下载和安装 Windows 客户端的 VPN 客户端包，以使更改应用于客户端。

> [!NOTE]
> 本文仅适用于 IKEv2。
>

## <a name="diagrams"></a>关于关系图

本文包含大量不同的关系图。 每节都介绍了不同的拓扑或配置。 考虑到本文的目的，站点到站点 (S2S) 和 VNet 到 VNet 连接的工作原理都相同，因为两者都是 IPsec 隧道。 本文中的所有 VPN 网关都基于路由。

## <a name="isolatedvnet"></a>独立 VNet

本示例中的点到站点 VPN 网关连接适用于未连接或未与其他任何虚拟网络 (VNet1) 对等互连的 VNet。 在此示例中，使用 SSTP 或 IKEv2 的客户端可以访问 VNet1。

![独立 VNet 路由](./media/vpn-gateway-about-point-to-site-routing/1.jpg "isolated VNet routing")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端可以访问 VNet1

* 非 Windows 客户端可以访问 VNet1

## <a name="multipeered"></a>多个对等互连 VNet

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 与 VNet2 对等互连。 VNet2 与 VNet3 对等互连。 VNet1 与 VNet4 对等互连。 VNet1 不与 VNet3 直接对等互连。 VNet1 已启用“允许网关传输”，VNet2 已启用“使用远程网关”。

使用 Windows 的客户端可以直接访问对等互连 VNet，但如果 VNet 对等互连或网络拓扑发生任何更改，必须重新下载 VPN 客户端。 非 Windows 客户端可直接访问对等互连 VNet。 访问不可传递，且仅限直接对等互连的 VNet。

![多个对等互连 VNet](./media/vpn-gateway-about-point-to-site-routing/2.jpg "multiple peered VNets")

### <a name="address-space"></a>地址空间：

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

* VNet4：10.4.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端可以访问 VNet1、VNet2 和 VNet4，但必须重新下载 VPN 客户端，以使拓扑更改生效。

* 非 Windows 客户端可以访问 VNet1、VNet2 和 VNet4

## <a name="multis2s"></a>使用 S2S VPN 连接的多个 VNet

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 之间没有直接的对等互连或站点到站点 VPN 连接。 所有站点到站点连接均未针对路由运行 BGP。

使用 Windows 或其他受支持 OS 的客户端只能访问 VNet1。 若要访问其他 VNet，必须使用 BGP。

![多个 VNet 和 S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "multiple VNets and S2S")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端只能访问 VNet1

* 非 Windows 客户端只能访问 VNet1

## <a name="multis2sbgp"></a>使用 S2S VPN 的多个 VNet (BGP)

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 之间没有直接的对等互连或站点到站点 VPN 连接。 所有站点到站点连接均针对路由运行 BGP。

使用 Windows 或其他受支持的 OS 的客户端可以访问使用站点到站点 VPN 连接连接的所有 VNet，但必须将到已连接 VNet 的路由手动添加到 Windows 客户端。

![多个 VNet 和 S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "multiple VNets and S2S BGP")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端可以访问 VNet1、VNet2 和 VNet3，但必须手动添加 VNet2 和 VNet3。

* 非 Windows 客户端可以访问 VNet1、VNet2 和 VNet3

## <a name="vnetbranch"></a>一个 VNet 和一个分支机构

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 不与其他任何虚拟网络连接/对等互连，但通过未运行 BGP 的站点到站点 VPN 连接连接到本地站点。

Windows 客户端可以访问 VNet1 和其他分支机构 (Site1)，但必须将到 Site1 的路由手动添加到客户端。 非 Windows 客户端可以访问 VNet1 以及本地 Site1。

![VNet 和分支机构的路由](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routing with a VNet and a branch office")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端只能访问 VNet1

* 非 Windows 客户端只能访问 VNet1

## <a name="vnetbranchbgp"></a>一个 VNet 和一个分支机构 (BGP)

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 不与其他任何虚拟网络连接/对等互连，但通过运行 BGP 的站点到站点 VPN 连接连接到本地站点 (Site1)。

Windows 客户端可以访问 VNet 和其他分支机构 (Site1)，但必须将到 Site1 的路由手动添加到客户端。 非 Windows 客户端可以访问 VNet 以及本地分支机构。

![一个 VNet 和一个分支机构 (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "one VNet and a branch office")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加到 Windows 客户端的路由：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端可以访问 VNet1 和 Site1，但必须手动添加到 Site1 的路由。

* 非 Windows 客户端可以访问 VNet1 和 Site1。


## <a name="multivnets2sbranch"></a>使用 S2S 和分支机构连接的多个 VNet

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 网络之间没有直接的对等互连或站点到站点 VPN 隧道。 VNet3 使用站点到站点 VPN 连接连接到分支机构 (Site1)。 所有 VPN 连接均未运行 BGP。

所有客户端都只能访问 VNet1。

![多 VNet S2S 和分支机构](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S and branch office")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加路由的客户端：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端只能访问 VNet1

* 非 Windows 客户端只能访问 VNet1

## <a name="multivnets2sbranchbgp"></a>使用 S2S 和分支机构连接的多个 VNet (BGP)

在此示例中，点到站点 VPN 网关连接适用于 VNet1。 VNet1 使用站点到站点 VPN 连接连接到 VNet2。 VNet2 使用站点到站点 VPN 连接连接到 VNet3。 VNet1 和 VNet3 网络之间没有直接的对等互连或站点到站点 VPN 隧道。 VNet3 使用站点到站点 VPN 连接连接到分支机构 (Site1)。 所有 VPN 连接均未运行 BGP。 所有 VPN 连接均运行 BGP。

使用 Windows 的客户端可以访问使用站点到站点 VPN 连接连接的 VNet 和站点，但必须将到 VNet2、VNet3 和 Site1 的路由手动添加到该客户端。 非 Windows 客户端可以访问使用站点到站点 VPN 连接连接的 VNet 和站点，而无需任何手动干预。 访问权限是可传递的，并且客户端可访问所有已连接 VNet 和站点（本地）中的资源。

![多 VNet S2S 和分支机构](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S and branch office")

### <a name="address-space"></a>地址空间

* VNet1：10.1.0.0/16

* VNet2：10.2.0.0/16

* VNet3：10.3.0.0/16

* Site1：10.101.0.0/16

### <a name="routes-added"></a>已添加的路由

* 已添加路由的客户端：10.1.0.0/16、192.168.0.0/24

* 已添加到非 Windows 客户端的路由：10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>Access

* Windows 客户端可以访问 VNet1、VNet2、VNet3 和 Site1，但必须将到 VNet2、VNet3 和 Site1 的路由手动添加到客户端。

* 非 Windows 客户端可以访问 VNet1、VNet2、VNet3 和 Site1。

## <a name="next-steps"></a>后续步骤

若要开始创建 P2S VPN，请参阅[使用 Azure 门户创建 P2S VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
