---
title: 使用 Azure 虚拟网络服务终结点保护对 Azure Cosmos DB 帐户的访问
description: 本文档介绍 Azure Cosmos 帐户的虚拟网络和子网访问控制。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 148a83cb57675e2e8bda8147041987180df998f0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037389"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>从虚拟网络访问 Azure Cosmos DB 资源

可将 Azure Cosmos 帐户配置为仅允许从虚拟网络 (VNET) 的特定子网进行访问。 启用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)来访问虚拟网络中子网上的 Azure Cosmos DB 后，来自该子网的流量将发送到具有该子网和虚拟网络的标识的 Azure Cosmos DB。 启用 Azure Cosmos DB 服务终结点后，可以通过将子网添加到 Azure Cosmos 帐户来限制对该子网的访问。

默认情况下，如果请求附带有效的授权令牌，则可从任何源访问 Azure Cosmos 帐户。 在 VNET 中添加一个或多个子网后，只有源自这些子网的请求才能获取有效响应。 源自其他任何源的请求将收到 404（未找到）响应。 

## <a name="frequently-asked-questions"></a>常见问题

下面是有关配置从虚拟网络进行访问的一些常见问题：

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>是否可以在 Azure Cosmos 帐户中同时指定虚拟网络服务终结点和 IP 访问控制策略？ 

可以在 Azure Cosmos 帐户中同时启用虚拟网络服务终结点和 IP 访问控制策略（也称为防火墙）。 这两个功能是互补的，共同确保 Azure Cosmos 帐户的隔离性和安全性。 使用 IP 防火墙可确保静态 IP 能够访问你的帐户。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>如何限制对虚拟网络中子网的访问？ 

需要执行两个步骤来限制从子网对 Azure Cosmos 帐户的访问。 首先，允许来自子网的流量将其子网和虚拟网络标识传递给 Azure Cosmos DB。 在子网中为 Azure Cosmos DB 启用服务终结点可实现此目的。 接下来，在 Azure Cosmos 帐户中添加一个规则，以便将此子网指定为可从中访问帐户的源。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>虚拟网络 ACL 和 IP 防火墙是否会拒绝请求或连接？ 

添加 IP 防火墙或虚拟网络访问规则后，只有来自受允许源的请求才能获取有效响应。 将拒绝其他请求并返回 404（未找到）错误。 必须将 Azure Cosmos 帐户的防火墙与连接级别的防火墙区分开来。 源仍可以连接到服务，连接本身不会遭到拒绝。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>在子网中为 Azure Cosmos DB 启用服务终结点后，我的请求开始遭到阻止。 发生了什么情况？

在子网中为 Azure Cosmos DB 启用服务终结点后，抵达帐户的流量源将从公共 IP 切换到虚拟网络和子网。 如果 Azure Cosmos 帐户仅包含基于 IP 的防火墙，则已启用服务的子网发出的流量将不再与 IP 防火墙规则相匹配，因此遭到拒绝。 请重温有关从基于 IP 的防火墙无缝迁移到基于虚拟网络的访问控制的步骤。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>对等互连的虚拟网络是否也有权访问 Azure Cosmos 帐户？ 
只有已添加到 Azure Cosmos 帐户的虚拟网络及其子网才拥有此访问权限。 将对等互连的虚拟网络中的子网添加到帐户之前，对等互连的 VNET 无权访问该帐户。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>最多允许多少个子网访问单个 Cosmos 帐户？ 
目前，最多允许 64 个子网访问一个 Azure Cosmos 帐户。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>是否可以启用从 VPN 和 Express Route 进行访问？ 
若要在本地通过 Express Route 访问 Azure Cosmos 帐户，需要启用 Microsoft 对等互连。 创建 IP 防火墙或虚拟网络访问规则后，可以在 Azure Cosmos 帐户 IP 防火墙中添加用于 Microsoft 对等互连的公共 IP 地址，以允许本地服务访问 Azure Cosmos 帐户。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>是否需要更新网络安全组 (NSG) 规则？ 
NSG 规则用于限制与虚拟网络中子网之间的连接。 将 Azure Cosmos DB 的服务终结点添加到子网时，无需在 NSG 中为 Azure Cosmos 帐户打开出站连接。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>服务终结点是否适用于所有 VNET？
否，只能为 Azure 资源管理器虚拟网络启用服务终结点。 经典虚拟网络不支持服务终结点。


## <a name="next-steps"></a>后续步骤

* [如何限制 Azure Cosmos 帐户访问虚拟网络中的子网](how-to-configure-vnet-service-endpoint.md)
* [如何为 Azure Cosmos 帐户配置 IP 防火墙](how-to-configure-firewall.md)

