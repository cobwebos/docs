---
title: Azure VMware 解决方案（按云简单 - 网络检查表）
description: 用于在 Azure VMware 解决方案上按云简单分配网络 CIDR 的检查表
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025004"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>按云简单实现 Azure VMware 解决方案的网络先决条件

云简单版 Azure VMware 解决方案提供了 VMware 私有云环境，可供本地环境、企业托管设备和 Azure 资源的用户和应用程序访问。 连接通过 VPN 和 Azure 快速路由连接等网络服务进行。 其中一些网络服务要求您指定网络地址范围以启用服务。 

本文中的表描述了使用指定地址的地址范围和相应服务集。 某些地址是强制性的，有些地址取决于要部署的服务。 这些地址空间不应与任何本地子网、Azure 虚拟网络子网或计划的 CloudSimple 工作负荷子网重叠。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>创建私有云所需的网络地址范围

在创建 CloudSimple 服务和私有云期间，必须遵守指定的无类域间路由 （CIDR） 范围，如下所示。

| 名称/用于     | 描述                                                                                                                            | 地址范围            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 网关 CIDR      | 边缘服务（VPN 网关）需要。  此 CIDR 在云简单服务创建期间是必需的，并且必须来自 RFC 1918 空间。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理网络需要。 必须在私有云创建期间指定此 CIDR。                                    | /24 或 /23 或 /22 或 /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Azure 连接到本地网络所需的网络地址范围

[通过 ExpressRoute 从本地网络连接到专用云网络](on-premises-connection.md)可建立全球覆盖连接。  该连接使用边界网关协议 （BGP） 在本地网络、私有云网络和 Azure 网络之间交换路由。

| 名称/用于             | 描述                                                                                                                                                                             | 地址范围 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| 快速路由对等 CIDR | 使用 ExpressRoute 全球覆盖实现本地连接时是必需的。 当通过支持票证发出全局覆盖连接请求时，必须提供此 CIDR。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>使用站点到站点 VPN 连接到本地网络所需的网络地址范围

[使用站点到本地 VPN 从本地网络连接到私有云网络](vpn-gateway.md)需要以下 IP 地址、本地网络和标识符。 

| 地址/地址范围 | 描述                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 对等 IP               | 本地 VPN 网关公共 IP 地址。 需要在本地数据中心和云简单服务区域之间建立站点到站点 VPN 连接。 在站点到站点 VPN 网关创建期间，需要此 IP 地址。                                         |
| 对等标识符       | 本地 VPN 网关的对等标识符。 这通常与**对等 IP**相同。  如果在本地 VPN 网关上指定了唯一标识符，则必须指定该标识符。  站点到站点 VPN 网关创建期间需要对等 ID。   |
| 本地网络   | 需要访问区域中的 CloudSimple 网络的本地前缀。  包括将访问 CloudSimple 网络的本地网络的所有前缀，包括用户从其中访问网络的客户端网络。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>使用点对点 VPN 连接所需的网络地址范围

点对点 VPN 连接允许从客户端计算机访问 CloudSimple 网络。  [要设置点对点 VPN，](vpn-gateway.md)必须指定以下网络地址范围。

| 地址/地址范围 | 描述                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 客户端子网         | 使用点对点 VPN 进行连接时，客户端子网提供 DHCP 地址。 当您在 CloudSimple 门户上创建点对点 VPN 网关时，需要此子网。  网络分为两个子网;一个用于 UDP 连接，另一个用于 TCP 连接。 |

## <a name="next-steps"></a>后续步骤

* [用于访问私有云的本地防火墙设置](on-premises-firewall-configuration.md)
* [快速入门 - 创建云简单服务](quickstart-create-cloudsimple-service.md)
* [快速入门 - 配置私有云](quickstart-create-private-cloud.md)
* 了解有关[Azure 网络连接](cloudsimple-azure-network-connection.md)的更多信息
* 了解有关[VPN 网关](cloudsimple-vpn-gateways.md)的更多
