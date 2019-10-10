---
title: Azure VMware 解决方案（按 CloudSimple）-网络清单
description: 在 Azure VMware 解决方案上通过 CloudSimple 分配网络 CIDR 的清单
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244822"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware 解决方案的网络必备组件（按 CloudSimple）

Azure VMware 解决方案（按 CloudSimple）提供了一个 VMware 私有云环境，该环境可供本地环境、企业托管设备和 Azure 资源中的用户和应用程序访问。 通过网络服务（例如 Vpn 和 Azure ExpressRoute 连接）提供连接。 其中某些网络服务要求你指定用于启用服务的网络地址范围。 

本文中的表介绍了使用指定地址的一组地址范围和相应服务。 某些地址是必需的，而有些则依赖于要部署的服务。 这些地址空间不应与任何本地子网、Azure 虚拟网络子网或计划 CloudSimple 工作负荷子网重叠。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>创建私有云所需的网络地址范围

在创建 CloudSimple 服务和私有云的过程中，必须符合指定的网络无类别域间路由（CIDR）范围，如下所示。

| 名称/用于     | 描述                                                                                                                            | 地址范围            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 网关 CIDR      | 对于 edge 服务（VPN 网关）是必需的。  此 CIDR 在 CloudSimple 服务创建过程中是必需的，并且必须来自 RFC 1918 空间。 | /28                      |
| vSphere/vSAN CIDR | 对于 VMware 管理网络是必需的。 在私有云创建过程中，必须指定此 CIDR。                                    | /24 或/23 或/22 或/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>与本地网络建立 Azure 网络连接所需的网络地址范围

[通过 ExpressRoute 从本地网络连接到私有云网络](on-premises-connection.md)建立 Global Reach 连接。  该连接使用边界网关协议（BGP）来交换本地网络、私有云网络和 Azure 网络之间的路由。

| 名称/用于             | 描述                                                                                                                                                                             | 地址范围 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute 对等 CIDR | 使用 ExpressRoute Global Reach 进行本地连接时需要。 当通过支持票证发出 Global Reach 连接请求时，必须提供此 CIDR。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>使用到本地网络的站点到站点 VPN 连接所需的网络地址范围

[使用站点到站点 VPN 从本地网络连接到私有云网络](vpn-gateway.md)需要以下 IP 地址：本地网络和标识符。 

| 地址/地址范围 | 描述                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 对等 IP               | 本地 VPN 网关公共 IP 地址。 需要在本地数据中心与 CloudSimple 服务区域之间建立站点到站点 VPN 连接。 此 IP 地址在站点到站点 VPN 网关创建期间是必需的。                                         |
| 对等标识符       | 本地 VPN 网关的对等标识符。 这通常与**对等 IP**相同。  如果在本地 VPN 网关上指定了唯一标识符，则必须指定该标识符。  在站点到站点 VPN 网关创建期间需要对等 ID。   |
| 本地网络   | 需要访问区域中 CloudSimple 网络的本地前缀。  包括将访问 CloudSimple 网络的本地网络中的所有前缀，包括用户将从其访问网络的客户端网络。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>使用点到站点 VPN 连接所需的网络地址范围

点到站点 VPN 连接允许从客户端计算机访问 CloudSimple 网络。  [若要设置点到站点 VPN](vpn-gateway.md)，则必须指定以下网络地址范围。

| 地址/地址范围 | 描述                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 客户端子网         | 使用点到站点 VPN 进行连接时，客户端子网会提供 DHCP 地址。 在 CloudSimple 门户上创建点到站点 VPN 网关时，需要使用此子网。  网络分为两个子网：一个用于 UDP 连接，另一个用于 TCP 连接。 |

## <a name="next-steps"></a>后续步骤

* [用于访问私有云的本地防火墙设置](on-premises-firewall-configuration.md)
* [快速入门-创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)
* [快速入门-配置私有云](quickstart-create-private-cloud.md)
* 了解有关[Azure 网络连接](cloudsimple-azure-network-connection.md)的详细信息
* 了解有关[VPN 网关](cloudsimple-vpn-gateways.md)的详细信息
