---
title: Azure 事件网格资源的网络安全
description: 本文介绍如何配置专用终结点的访问权限
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 1887b6b5919a8b0f6e8f570b2471d74d9541df31
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119236"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure 事件网格资源的网络安全
本文介绍如何将以下安全功能与 Azure 事件网格配合使用： 

- 出口服务标记
- 针对入口的 IP 防火墙规则（预览版）
- 入口的专用终结点


## <a name="service-tags"></a>服务标记
服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量减少频繁更新网络安全规则所需的复杂操作。 有关服务标记的详细信息，请参阅[服务标记概述](../virtual-network/service-tags-overview.md)。

可以在[网络安全组](../virtual-network/security-overview.md#security-rules) 或  [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制。 创建安全规则时，请使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 AzureEventGrid****），即可允许或拒绝相应服务的流量。**  **  

| 服务标记 | 目的 | 可以使用入站还是出站连接？ | 可以支持区域范围？ | 是否可在 Azure 防火墙中使用？ |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure 事件网格。 | 推送、请求和匿名 | 否 | 否 |


## <a name="ip-firewall"></a>IP 防火墙 
Azure 事件网格支持对发布到主题和域进行基于 IP 的访问控制。 使用基于 IP 的控制，可以将主题和域的发布者限制为一组经过批准的计算机和云服务。 此功能是对事件网格支持的[身份验证机制](security-authentication.md)的补充。

默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问主题和域。 使用 IP 防火墙，可以将其进一步限制为采用 [CIDR（无类别域际路由选择）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法的一组 IP 地址或 IP 地址范围。 来自任何其他 IP 地址的发布者都将被拒绝，并将收到 403（禁止）响应。

有关为主题和域配置 IP 防火墙的分步说明，请参阅[配置 ip 防火墙](configure-firewall.md)。

## <a name="private-endpoints"></a>专用终结点
你可以使用[专用终结点](../private-link/private-endpoint-overview.md)将事件直接从你的虚拟网络直接发送到你的主题和域，[而无需](../private-link/private-link-overview.md)通过公共 internet。 专用终结点是用于 VNet 中的 Azure 服务的特殊网络接口。 为主题或域创建专用终结点时，它会在 VNet 上的客户端与事件网格资源之间提供安全连接。 从 VNet 的 IP 地址范围为专用终结点分配 IP 地址。 专用终结点与事件网格服务之间的连接使用安全的专用链接。

![体系结构关系图](./media/network-security/architecture-diagram.png)

通过为事件网格资源使用专用终结点，你可以：

- 通过 Microsoft 主干网络（而不是公共 Internet）从 VNet 安全地访问主题或域。
- 使用 VPN 或 ExpressRoutes 通过专用对等互连从连接到 VNet 的本地网络安全地进行连接。

在 VNet 中创建用于主题或域的专用终结点时，系统会将申请批准的许可请求发送给资源所有者。 如果请求创建专用终结点的用户也是资源的所有者，则此许可请求会自动获得批准。 否则，连接将处于挂起**** 状态，直到获得批准。 VNet 中的应用程序可以使用通过其他方式连接时所用的相同连接字符串和授权机制，通过专用终结点无缝地连接到事件网格服务。 资源所有者可以通过 Azure 门户中资源的“专用终结点”**** 选项卡来管理许可请求和专用终结点。

### <a name="connect-to-private-endpoints"></a>连接到专用终结点
使用专用终结点的 VNet 上的发布者应该为主题或域使用与连接到公共终结点的客户端相同的连接字符串。 DNS 解析会通过专用链接自动将连接从 VNet 路由到主题或域。 默认情况下，事件网格会创建一个附加到 VNet 的[专用 DNS 区域](../dns/private-dns-overview.md)，并带有专用终结点的必要更新。 但是，如果使用自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。

### <a name="dns-changes-for-private-endpoints"></a>专用终结点的 DNS 更改
创建专用终结点时，资源的 DNS CNAME 记录将更新为子域中具有前缀 `privatelink` 的别名。 默认情况下，系统会创建一个对应于专用链接的子域的专用 DNS 区域。 

使用专用终结点从 VNet 外部解析主题或域终结点 URL 时，它会解析为服务的公共终结点。 从托管专用终结点的 VNet 外部**** 进行解析时，“topicA”的 DNS 资源记录将为：

| 名称                                          | 类型      | Value                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

可以使用 [IP 防火墙](#ip-firewall)通过公共终结点拒绝或控制 VNet 外部的客户端的访问。 

从托管专用终结点的 VNet 进行解析时，主题或域终结点 URL 解析为专用终结点的 IP 地址。 从托管专用终结点的 VNet 内部**** 解析时，“topicA”主题的 DNS 资源记录将为：

| 名称                                          | 类型      | Value                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

对于托管专用终结点的 VNet 上的客户端和 VNet 外部的客户端，此方法允许使用相同的连接字符串访问主题或域。

如果在网络上使用自定义 DNS 服务器，则客户端可以将主题或域终结点的 FQDN 解析为专用终结点 IP 地址。 配置 DNS 服务器以将专用链接子域委托到 VNet 的专用 DNS 区域，或者使用专用终结点 IP 地址为 `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` 配置 A 记录。

建议 DNS 区域名称为 `privatelink.eventgrid.azure.net`。

### <a name="private-endpoints-and-publishing"></a>专用终结点和发布

下表介绍专用终结点连接的各种状态以及对发布的影响：

| 连接状态   |  成功发布（是/否） |
| ------------------ | -------------------------------|
| 已批准           | 是                            |
| 已拒绝           | 否                             |
| 挂起            | 否                             |
| 已断开连接       | 否                             |

若要成功进行发布，专用终结点连接状态应为“已批准”****。 如果连接被拒绝，则无法使用 Azure 门户批准该连接。 唯一可行的做法是删除连接并创建一个新的连接。

## <a name="pricing-and-quotas"></a>定价和配额
专用终结点**** 在事件网格的基本层和高级层中均可用。 事件网格最多允许每个主题或域创建 64 个专用终结点连接。 

事件网格的基本层和高级层都提供 IP 防火墙**** 功能。 我们最多允许每个主题或域创建 16 条 IP 防火墙规则。

## <a name="next-steps"></a>后续步骤
你可以将事件网格资源的 IP 防火墙配置为仅允许从一组选择的 IP 地址或 IP 地址范围通过公共 internet 进行访问。 有关分步说明，请参阅[配置 IP 防火墙](configure-firewall.md)。

你可以配置专用终结点，以仅从所选的虚拟网络中限制访问。 有关分步说明，请参阅[配置专用终结点](configure-private-endpoints.md)。

若要解决网络连接问题，请参阅[排查网络连接问题](troubleshoot-network-connectivity.md)
