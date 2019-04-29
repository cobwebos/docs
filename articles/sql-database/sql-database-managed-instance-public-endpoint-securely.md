---
title: 保护公共终结点托管的实例-Azure SQL 数据库托管的实例 |Microsoft Docs
description: 在 Azure 中安全地使用公共终结点，与托管实例
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: vanto, carlrab
manager: digimobile
origin.date: 04/16/2019
ms.date: 04/29/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315032"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>使用 Azure SQL 数据库托管实例安全地与公共终结点

未能启用托管的实例通过提供用户连接的 azure SQL 数据库[公共终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 本文指导如何使此配置更安全。

## <a name="scenarios"></a>方案

托管的实例提供了专用终结点，以便从其虚拟网络中的连接。 默认选项是提供最大的隔离。 但是，有的方案需要一个公共终结点连接的位置：

- 与多租户仅 PaaS 产品/服务的集成。
- 更高的吞吐量的数据交换比使用 VPN。
- 公司策略禁止在企业网络内的 PaaS。

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>部署用于公开的终结点访问的托管的实例

尽管不是必需的但具有公共终结点访问的托管实例的常见部署模型是专用的独立虚拟网络中创建实例。 在此配置中，使用虚拟网络，只需为虚拟群集隔离。 如果与企业网络 IP 地址空间重叠的托管的实例 IP 地址空间，它是不相关。

## <a name="securing-data-in-motion"></a>保护移动数据

如果客户端驱动程序支持加密，托管的实例数据流量会始终加密。 托管的实例与其他 Azure 虚拟机或 Azure 服务之间的数据不会离开 Azure 的主干。 如果没有在本地网络连接到的托管的实例，建议使用 Express Route 使用 Microsoft 对等互连。 快速路由，有助于避免通过公共 Internet 移动数据 （对于托管的实例的专用连接，仅专用对等互连可以使用）。

## <a name="locking-down-inbound-and-outbound-connectivity"></a>阻止入站和出站连接

下图显示了建议的安全配置。

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

托管的实例都有[专用公共终结点地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。 此 IP 地址应在客户端的出站防火墙和网络安全组规则设置，以限制出站连接。

若要确保托管实例的流量来自受信任的源，建议从与已知的 IP 地址的源连接。 访问限制为端口 3342 上的托管的实例公共终结点使用网络安全组。

当客户端需要从本地网络启动连接时，请确保起始地址转换为一组已知良好的 Ip。 如果，不能实现 （例如，在典型的方案中的移动工作人员），建议使用[点到站点 VPN 连接和专用终结点](sql-database-managed-instance-configure-p2s.md)。

如果连接从 Azure 中启动，则建议它流量来自已知分配[VIP](../virtual-network/virtual-networks-reserved-public-ip.md) （例如，虚拟机）。 为便于管理 VIP 地址，客户可以考虑使用[公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)。