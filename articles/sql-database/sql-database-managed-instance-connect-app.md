---
title: Azure SQL 数据库托管实例 - 连接应用程序 | Microsoft Docs
description: 本文介绍如何将应用程序连接到 Azure SQL 数据库托管实例。
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: bea1dc88d66717717cdeacbc8504f5df7e37ba04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647827"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>将应用程序连接到 Azure SQL 数据库托管实例

在确定如何以及在何处托管应用程序时，目前有多个选项可供选择。 
 
可以选择使用 Azure 应用服务或 Azure 的某些虚拟网络 (VNet) 集成选项（例如 Azure 应用服务环境、虚拟机、虚拟机规模集）将应用程序托管在云中。 还可以采用混合云方法，将应用程序保留在本地。 
 
不管选择哪个选项，都可将应用程序连接到托管实例（预览版）。  

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>在同一 VNet 中连接应用程序 

这是最简单的方案。 即使位于不同子网，VNet 中的虚拟机也能直接相互连接。 这意味着，只需相应地设置连接字符串，就能连接 Azure 应用程序环境或虚拟机中的应用程序。  
 
如果无法建立连接，请检查是否在应用程序子网中设置了网络安全组。 如果设置了网络安全组，则需要在 SQL 端口 1433 上打开出站连接，并打开 11000-12000 端口范围，以便重定向。 

## <a name="connect-an-application-inside-a-different-vnet"></a>在不同的 VNet 中连接应用程序 

此方案略微复杂，因为托管实例在其自身的 VNet 中具有专用 IP 地址。 若要建立连接，应用程序需要访问部署了托管实例的 VNet。 因此，首先需要在应用程序与托管实例 VNet 之间建立连接。 要正常实施此方案，VNet 不一定要位于同一个订阅中。 
 
可通过两个选项连接 VNet： 
- [Azure 虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md) 
- VNet 到 VNet VPN 网关（[Azure 门户](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)） 
 
对等互连是首选的选项，因为对等互连使用 Microsoft 主干网络，因此，从连接角度看，对等互连 VNet 中的虚拟机与同一 VNet 中虚拟机之间的延迟没有明显差别。 VNet 对等互连限于相同区域内的网络。  
 
> [!IMPORTANT]
> 由于[全球虚拟网络对等互连的限制](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)，用于托管实例的 VNet 对等互连方案限于相同区域内的网络。 

## <a name="connect-an-on-premises-application"></a>连接本地应用程序 

只能通过专用 IP 地址访问托管实例。 若要从本地访问它，需在应用程序与托管实例 VNet 之间建立站点到站点连接。 
 
可以使用两个选项从本地连接到 Azure VNet： 
- 站点到站点 VPN 连接（[Azure 门户](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)） 
- [ExpressRoute](../expressroute/expressroute-introduction.md) 连接  
 
如果已成功建立本地到 Azure 的连接，但无法与托管实例建立连接，请检查防火墙中是否在 SQL 端口 1433 上打开了出站连接，并且打开了 11000-12000 端口范围，以便重定向。 

## <a name="connect-an-azure-app-service-hosted-application"></a>连接 Azure 应用服务托管应用程序 

只能通过专用 IP 地址访问托管实例，因此，若要从 Azure 应用服务访问托管实例，首先需要在应用程序与托管实例 VNet 之间建立连接。 请参阅[将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)。  
 
有关故障排除信息，请参阅[排查 VNet 和应用程序问题](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)。 如果无法建立连接，请尝试[同步网络配置](sql-database-managed-instance-sync-network-configuration.md)。 
 
将 Azure 应用服务连接到托管实例的一种特殊情况是将 Azure 应用服务集成到已与托管实例 VNet 建立对等互连的网络。 对于这种情况，需要设置以下配置： 

- 托管实例 VNet 不得使用网关  
- 托管实例 VNet 中必须设置“使用远程网关”选项 
- 对等互连的 VNet 中必须设置“允许网关传输”选项 
 
下图演示了此方案：

![集成的应用对等互连](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>在开发人员工具箱中连接应用程序 

只能通过专用 IP 地址访问托管实例，因此，若要从开发人员工具箱访问托管实例，首先需要在开发人员工具箱与托管实例 VNet 之间建立连接。  
 
参考有关本机 Azure 证书身份验证的文章中的详细说明，配置与 VNet 的点到站点连接（[Azure 门户](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)、[PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)、[Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)）。  

## <a name="next-steps"></a>后续步骤

- 有关托管实例的信息，请参阅[什么是托管实例](sql-database-managed-instance.md)。
- 有关演示如何新建托管实例的教程，请参阅[创建托管实例](sql-database-managed-instance-create-tutorial-portal.md)。
