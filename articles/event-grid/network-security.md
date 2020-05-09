---
title: Azure 事件网格资源的网络安全
description: 本文介绍如何配置专用终结点的访问权限
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: d6d6d8df8f3c5da762ac672b304ec072a723e7d7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857047"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure 事件网格资源的网络安全
本文介绍如何通过 Azure 事件网格使用以下安全功能： 

- 出口的服务标记（预览）
- 用于入口的 IP 防火墙规则（预览）
- 入口的专用终结点（预览）


## <a name="service-tags"></a>服务标记
服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 管理服务标记包含的地址前缀，并在地址发生更改时自动更新服务标记，从而最大程度地降低频繁更新网络安全规则的复杂性。 有关服务标记的详细信息，请参阅[服务标记概述](../virtual-network/service-tags-overview.md)。

可以在[网络安全组](../virtual-network/security-overview.md#security-rules) 或  [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制。 创建安全规则时，请使用服务标记代替特定 IP 地址。 通过在规则的相应 "*源* " 或 " *目标* " 字段中指定服务标记名称（例如， **AzureEventGrid**），可以允许或拒绝相应服务的流量。

| 服务标记 | 目的 | 可以使用入站还是出站连接？ | 可以支持区域范围？ | 是否可在 Azure 防火墙中使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure 事件网格。 <br/><br/>*注意：* 此标记涵盖美国中南部、美国东部、美国东部2、美国西部2和美国中部的 Azure 事件网格端点。 | 推送、请求和匿名 | 否 | 否 |


## <a name="ip-firewall"></a>IP 防火墙 
Azure 事件网格支持基于 IP 的访问控制，可用于发布到主题和域。 使用基于 IP 的控件，你可以将发布者限制为主题或域，仅允许一组经过批准的一组计算机和云服务。 此功能对事件网格支持的[身份验证机制](security-authentication.md)进行了补充。

默认情况下，只要请求附带有效的身份验证和授权，就可以从 internet 访问主题和域。 使用 IP 防火墙，你可以将其进一步限制为仅一组 IP 地址或[CIDR （无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的 ip 地址范围。 源自任何其他 IP 地址的发布者将被拒绝，并将收到403（禁止）响应。


## <a name="private-endpoints"></a>专用终结点
你可以使用[专用终结点](../private-link/private-endpoint-overview.md)将事件直接从你的虚拟网络直接发送到你的主题和域，[而无需](../private-link/private-link-overview.md)通过公共 internet。 专用终结点是 VNet 中 Azure 服务的特殊网络接口。 为主题或域创建专用终结点时，它会在 VNet 中的客户端和事件网格资源之间提供安全连接。 专用终结点是从 VNet 的 IP 地址范围分配的 IP 地址。 专用终结点和事件网格服务之间的连接使用安全的专用链接。

![体系结构关系图](./media/network-security/architecture-diagram.png)

使用事件网格资源的专用终结点可以：

- 通过 Microsoft 骨干网络（而不是公共 internet）从 VNet 安全访问你的主题或域。
- 使用 VPN 或 ExpressRoutes 与专用对等互连从连接到 VNet 的本地网络安全地连接。

在 VNet 中创建主题或域的专用终结点时，会将同意请求发送给资源所有者。 如果请求创建专用终结点的用户也是该资源的所有者，则会自动批准此同意请求。 否则，连接在批准之前处于 "**挂起**" 状态。 VNet 中的应用程序可以使用相同的连接字符串和它们将使用的授权机制，以无缝方式连接到专用终结点上的事件网格服务。 资源所有者可以通过 Azure 门户中资源的 "**专用终结点**" 选项卡来管理同意请求和专用终结点。

### <a name="connect-to-private-endpoints"></a>连接到专用终结点
使用专用终结点的 VNet 中的发布服务器在连接到公共终结点的客户端时，应使用与主题或域相同的连接字符串。 DNS 解析通过专用链接自动将从 VNet 到主题或域的连接路由。 默认情况下，事件网格创建附加到 VNet 的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的必要更新。 但是，如果使用自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。

### <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改
创建专用终结点时，会将资源的 DNS CNAME 记录更新为具有前缀`privatelink`的子域中的别名。 默认情况下，将创建一个专用 DNS 区域，该区域对应于专用链接的子域。 

当你将主题或域终结点 URL 从 VNet 外部解析到专用终结点时，它将解析为服务的公共终结点。 当从承载专用终结点**的 VNet 之外**解析时，"topicA" 的 DNS 资源记录将为：

| 名称                                          | 类型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure 流量管理器配置文件\>

你可以通过使用[IP 防火墙](#ip-firewall)的公共终结点，拒绝或控制对 VNet 之外的客户端的访问。 

当从承载专用终结点的 VNet 解析时，主题或域终结点 URL 解析为专用终结点的 IP 地址。 当从承载专用终结点**的 VNet**中解析时，主题 "topicA" 的 DNS 资源记录将为：

| 名称                                          | 类型      | 值                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

此方法允许对承载专用终结点的 VNet 中的客户端和 VNet 之外的客户端使用相同的连接字符串来访问主题或域。

如果在网络上使用自定义 DNS 服务器，则客户端可以将主题或域终结点的 FQDN 解析到专用终结点 IP 地址。 将你的 DNS 服务器配置为将专用链接子域委托给 VNet 的专用 DNS 区域，或`topicOrDomainName.regionName.privatelink.eventgrid.azure.net`使用专用终结点 IP 地址配置 A 记录。

建议的 DNS 区域名称为`privatelink.eventgrid.azure.net`。

### <a name="private-endpoints-and-publishing"></a>专用终结点和发布

下表描述了专用终结点连接的各种状态以及对发布的影响：

| 连接状态   |  已成功发布（是/否） |
| ------------------ | -------------------------------|
| 已批准           | 是                            |
| 已拒绝           | 否                             |
| Pending            | 否                             |
| 已断开连接       | 否                             |

若要成功发布，应**批准**专用终结点连接状态。 如果连接被拒绝，则无法使用 Azure 门户进行批准。 唯一的可能性是删除连接并改为创建一个新连接。

## <a name="pricing-and-quotas"></a>定价和配额
在事件网格的 "基本" 和 "高级" 层中提供了**专用终结点**。 事件网格允许每个主题或域创建多达64的专用终结点连接。 

事件网格的 "基本" 和 "高级" 层中提供了**IP 防火墙**功能。 对于每个主题或域，最多允许创建16个 IP 防火墙规则。

## <a name="next-steps"></a>后续步骤
你可以将事件网格资源的 IP 防火墙配置为仅允许从一组选择的 IP 地址或 IP 地址范围通过公共 internet 进行访问。 有关分步说明，请参阅[配置 IP 防火墙](configure-firewall.md)。

你可以配置专用终结点，以仅从所选的虚拟网络中限制访问。 有关分步说明，请参阅[配置专用终结点](configure-private-endpoints.md)。
