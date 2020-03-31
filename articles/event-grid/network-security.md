---
title: Azure 事件网格资源的网络安全
description: 本文介绍如何配置来自专用终结点的访问
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300149"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure 事件网格资源的网络安全
本文介绍如何使用以下安全功能与 Azure 事件网格： 

- 出口服务标签（预览）
- 入口的 IP 防火墙规则（预览）
- 入口专用终结点（预览）


## <a name="service-tags"></a>服务标记
服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而最大限度地减少频繁更新网络安全规则的复杂性。 有关服务标记的详细信息，请参阅[服务标记概述](../virtual-network/service-tags-overview.md)。

可以使用服务标记在[网络安全组](../virtual-network/security-overview.md#security-rules) 或 [Azure 防火墙](../firewall/service-tags.md)上定义网络访问控件。 创建安全规则时，使用服务标记代替特定的 IP 地址。 通过在规则的相应*源* 或 *目标* 字段中指定服务标记名称（例如 **，AzureEventGrid），** 可以允许或拒绝相应服务的流量。

| 服务标记 | 目的 | 可以使用入站或出站吗？ | 可以是区域性的吗？ | 是否可在 Azure 防火墙中使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Azure 事件网格 | Azure 事件网格。 <br/><br/>*注：* 此标记仅涵盖美国中南部、美国东部、美国东部 2、美国西部 2 和美国中部中的 Azure 事件网格终结点。 | 推送、请求和匿名 | 否 | 否 |


## <a name="ip-firewall"></a>IP 防火墙 
Azure 事件网格支持基于 IP 的访问控件，用于发布到主题和域。 使用基于 IP 的控件，可以将发布者限制为主题或域，而只能将一组经过批准的计算机和云服务。 此功能补充事件网格支持的[身份验证机制](security-authentication.md)。

默认情况下，只要请求附带有效的身份验证和授权，主题和域都可以从 Internet 访问。 使用 IP 防火墙，您可以进一步将其限制为[CIDR（无类域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的一组 IP 地址或 IP 地址范围。 来自任何其他 IP 地址的发布者将被拒绝，并将收到 403（禁止）响应。


## <a name="private-endpoints"></a>专用终结点
您可以使用[专用终结点](../private-link/private-endpoint-overview.md)允许事件直接从虚拟网络通过[专用链路](../private-link/private-link-overview.md)安全地进入您的主题和域，而无需通过公共互联网。 专用终结点是 VNet 中 Azure 服务的特殊网络接口。 为主题或域创建专用终结点时，它将在 VNet 上的客户端和事件网格资源之间的客户端之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配了 IP 地址。 专用终结点和事件网格服务之间的连接使用安全的专用链路。

![体系结构关系图](./media/network-security/architecture-diagram.png)

为事件网格资源使用专用终结点使您能够：

- 通过 Microsoft 骨干网络（而不是公共互联网）从 VNet 安全地访问您的主题或域。
- 使用 VPN 或快速路由使用专用对等互连从连接到 VNet 的本地网络进行安全连接。

在 VNet 中为主题或域创建专用终结点时，将发送同意请求以向资源所有者发送审批请求。 如果请求创建私有终结点的用户也是资源的所有者，则此同意请求将自动获得批准。 否则，连接处于**挂起**状态，直到获得批准。 VNet 中的应用程序可以使用它们将以其他方式使用的相同连接字符串和授权机制，通过专用终结点无缝连接到事件网格服务。 资源所有者可以通过 Azure 门户中资源的**专用终结点**选项卡管理同意请求和专用终结点。

### <a name="connect-to-private-endpoints"></a>连接到专用终结点
使用专用终结点的 VNet 上的发布者应对主题或域使用与连接到公共终结点的客户端相同的连接字符串。 DNS 解析通过专用链路自动路由从 VNet 到主题或域的连接。 默认情况下，事件网格创建附加到 VNet 的[专用 DNS 区域](../dns/private-dns-overview.md)，并针对专用终结点进行必要的更新。 但是，如果您使用的是自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。

### <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改
创建专用终结点时，资源的 DNS CNAME 记录将更新为子域中的别名，该别名的前缀`privatelink`为 。 默认情况下，将创建与专用链接子域对应的专用 DNS 区域。 

当您使用专用终结点从 VNet 外部解析主题或域终结点 URL 时，它将解析为服务的公共终结点。 从托管专用终结点**的 VNet 外部**解析时，"主题 A"的 DNS 资源记录将是：

| “属性”                                          | 类型      | “值”                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure 流量管理器配置文件\>

您可以使用[IP 防火墙](#ip-firewall)通过公共终结点拒绝或控制 VNet 外部客户端的访问。 

从托管专用终结点的 VNet 解析时，主题或域终结点 URL 解析为专用终结点的 IP 地址。 主题"主题 A"的 DNS 资源记录在从托管私有终结点**的 VNet 内部**解析时，将为：

| “属性”                                          | 类型      | “值”                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

此方法允许对承载专用终结点的 VNet 上的客户端和 VNet 外部的客户端使用相同的连接字符串访问主题或域。

如果您在网络上使用自定义 DNS 服务器，客户端可以将主题的 FQDN 或域终结点解析为专用终结点 IP 地址。 配置 DNS 服务器以将专用链接子域委派为 VNet 的专用 DNS 区域，或使用专用终结点`topicOrDomainName.regionName.privatelink.eventgrid.azure.net`IP 地址配置 A 记录。

建议的 DNS 区域名称`privatelink.eventgrid.azure.net`为 。

### <a name="private-endpoints-and-publishing"></a>专用终结点和发布

下表描述了专用终结点连接的各种状态及其对发布的影响：

| 连接状态   |  成功发布（是/否） |
| ------------------ | -------------------------------|
| 已批准           | 是                            |
| 已拒绝           | 否                             |
| 挂起的            | 否                             |
| 已断开连接       | 否                             |

要成功发布，应**批准**私有终结点连接状态。 如果连接被拒绝，则无法使用 Azure 门户批准连接。 唯一的可能性是删除连接并创建一个新的连接。

## <a name="pricing-and-quotas"></a>定价和配额
**专用终结点**仅适用于高级层主题和域。 事件网格允许每个主题或域最多创建 64 个专用终结点连接。 要从基本层升级到高级层，请参阅[更新定价层](update-tier.md)一文。

**IP 防火墙**功能在事件网格的基本层和高级层中都可用。 我们允许每个主题或域最多创建 16 个 IP 防火墙规则。


## <a name="next-steps"></a>后续步骤
您可以为事件网格资源配置 IP 防火墙，以便仅从一组选定的 IP 地址或 IP 地址范围限制通过公共 Internet 进行访问。 有关分步说明，请参阅配置 IP[防火墙](configure-firewall.md)。

您可以配置专用终结点以仅限制从选定的虚拟网络进行访问。 有关分步说明，请参阅[配置专用终结点](configure-private-endpoints.md)。
