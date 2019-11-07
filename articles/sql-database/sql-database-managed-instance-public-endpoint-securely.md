---
title: 安全托管实例公共终结点-Azure SQL 数据库托管实例
description: 在 Azure 中安全使用包含托管实例的公共终结点
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: 3d97eb16ee2a8b3203b187dca7b2a17974473098
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687949"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>在公共终结点中安全使用 Azure SQL 数据库托管实例

Azure SQL 数据库托管实例可以通过[公共终结点](../virtual-network/virtual-network-service-endpoints-overview.md)提供用户连接。 本文将介绍如何提高此配置的安全性。

## <a name="scenarios"></a>方案

SQL 数据库托管实例提供专用终结点用于从其虚拟网络内部启用连接。 默认选项是提供最大的隔离性。 但在某些情况下，需要提供公共终结点连接：

- 托管实例必须与仅限多租户的平台即服务 (PaaS) 产品/服务集成。
- 所需的数据交换吞吐量高于 VPN 所能提供的吞吐量。
- 公司政策禁止在企业网络中使用 PaaS。

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>部署托管实例以访问公共终结点

可以访问公共终结点的托管实例的常用部署模型是在专用的隔离虚拟网络中创建实例，但不一定非要这样做。 在此配置中，虚拟网络只是用于实现虚拟群集隔离。 托管实例 IP 地址空间是否与企业网络 IP 地址空间重叠并不重要。

## <a name="secure-data-in-motion"></a>保护动态数据

如果客户端驱动程序支持加密，则始终加密托管实例数据流量。 在托管实例与其他 Azure 虚拟机或 Azure 服务之间发送的数据永远不会离开 Azure 主干网络。 如果托管实例与本地网络之间已建立连接，则我们建议配合使用 Azure ExpressRoute 和 Microsoft 对等互连。 ExpressRoute 有助于避免通过公共 Internet 移动数据。 对于托管实例专用连接，只能使用专用对等互连。

## <a name="lock-down-inbound-and-outbound-connectivity"></a>锁定入站和出站连接

下图显示了建议的安全配置：

![用于锁定入站和出站连接的安全配置](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

托管实例具有[专用公共终结点地址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。 在客户端出站防火墙和网络安全组规则中，设置此公共终结点 IP 地址以限制出站连接。

为了确保发往托管实例的流量来自受信任的源，我们建议使用已知的 IP 地址从源建立连接。 使用网络安全组限制对端口 3342 上的托管实例公共终结点的访问。

如果客户端需要从本地网络发起连接，请确保发起地址可转换为一组已知的 IP 地址。 如果无法做到这一点（例如，移动工作者就是一个典型的示例），我们建议使用[点到站点 VPN 连接和专用终结点](sql-database-managed-instance-configure-p2s.md)。

如果连接是从 Azure 发起的，我们建议从分配的已知[虚拟 IP 地址](../virtual-network/virtual-networks-reserved-public-ip.md)（例如虚拟机）发出流量。 为便于管理虚拟 IP (VIP) 地址，建议使用[公共 IP 地址前缀](../virtual-network/public-ip-address-prefix.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何为管理实例配置公共终结点：[配置公共终结点](sql-database-managed-instance-public-endpoint-configure.md)
