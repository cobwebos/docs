---
title: Azure DNS 常见问题解答
description: 有关 Azure DNS 的常见问题
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/16/2019
ms.author: victorh
ms.openlocfilehash: a83ded660b56028ea311992ba6161e8a8e43f65d
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511966"
---
# <a name="azure-dns-faq"></a>Azure DNS 常见问题解答

## <a name="about-azure-dns"></a>关于 Azure DNS

### <a name="what-is-azure-dns"></a>什么是 Azure DNS？

域名系统 (DNS) 将网站或服务名称转换或解析为它的 IP 地址。 Azure DNS 是 DNS 域的托管服务。 它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全球网络上。 此系统使用任意广播网络，以便每个 DNS 查询由最近的可用 DNS 服务器来应答。 Azure DNS 为域提供更快的性能和高可用性。

Azure DNS 基于 Azure 资源管理器。 Azure DNS 可以利用资源管理器功能，例如基于角色的访问控制、审核日志和资源锁定。 可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 来管理域和记录。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务集成。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 的费用是多少？

Azure DNS 计费模型基于 Azure DNS 中托管的 DNS 区域数。 此外，它基于这些区域接收的 DNS 查询数。 根据使用情况提供折扣。

有关详细详细，请参阅 [Azure DNS 定价页](https://azure.microsoft.com/pricing/details/dns/)。

### <a name="what-is-the-sla-for-azure-dns"></a>什么是 SLA for Azure DNS？

Azure 保证在任何情况下，有效的 DNS 请求将从至少一个 Azure DNS 名称服务器中收到响应。

有关详细详细，请参阅 [Azure DNS SLA 页](https://azure.microsoft.com/support/legal/sla/dns)。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>什么是 DNS 区域？ 它是否等同于 DNS 域？ 

域在域名系统中具有唯一名称。 例如 contoso.com。

DNS 区域用来托管某个特定域的 DNS 记录。 例如，域 contoso.com 可能包含几条 DNS 记录。 这些记录可能包含 mail.contoso.com（用于邮件服务器）和 www.contoso.com（用于网站）。 这些记录托管在 DNS 区域 contoso.com 中。

域名仅是一个名称。 DNS 区域是包含域名的 DNS 记录的数据资源。 可以使用 Azure DNS 托管 DNS 区域，以及管理 Azure 中域的 DNS 记录。 它还提供 DNS 名称服务器，用于回答来自 Internet 的 DNS 查询。

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>是否需要购买 DNS 域名才能使用 Azure DNS？ 

不一定。

无需购买域即可托管 Azure DNS 中的 DNS 区域。 没有域名也可随时创建 DNS 区域。 仅当此区域的 DNS 查询定向到分配给该区域的 Azure DNS 名称服务器时，才会解析这些查询。

若要将 DNS 区域链接到全局 DNS 层级结构，则必须购买域名。 然后，来自全球任意位置的 DNS 查询会使用 DNS 记录来查找 DNS 区域和做出应答。

## <a name="azure-dns-features"></a>Azure DNS 功能

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>在流量管理器中使用域名顶点的别名记录时，是否存在任何限制？

是的。 必须在 Azure 流量管理器中使用静态公共 IP 地址。 使用静态 IP 地址配置**外部终结点**目标。 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS 是否支持基于 DNS 的流量路由或终结点故障转移？

流量管理器提供基于 DNS 的流量路由和终结点故障转移。 流量管理器是一项独立服务，可与 Azure DNS 配合使用。 有关详细信息，请参阅[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。

Azure DNS 仅支持托管静态 DNS 域，其中对某给定的 DNS 记录来说，每个 DNS 查询始终接收相同的 DNS 响应。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS 是否支持域名注册？

不是。 Azure DNS 目前不支持购买域名。 若要购买域，必须使用第三方域名注册机构。 注册机构通常收取小额年费。 然后，可将域托管在 Azure DNS 中用于管理 DNS 记录。 有关详细信息，请参阅 [向 Azure DNS 委派域](dns-domain-delegation.md)。

我们正在 Azure 积压工作中跟踪域购买功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS 是否支持 DNSSEC？

不是。 Azure DNS 目前不支持域名系统安全扩展 (DNSSEC)。

我们正在 Azure DNS 积压工作中跟踪 DNSSEC 功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS 是否支持区域传送 (AXFR/IXFR)？

不是。 Azure DNS 目前不支持区域传送。 可[使用 Azure CLI 将 DNS 区域导入 Azure DNS](dns-import-export.md)。 然后，可通过 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)来托管 DNS 记录。

我们正在 Azure DNS 积压工作中跟踪区域传送功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS 是否支持 URL 重定向？

不是。 URL 重定向服务实际并非 DNS 服务。 它们在 HTTP 级别而非 DNS 级别运行。 某些 DNS 提供商会在整体产品/服务中捆绑销售 URL 重定向服务。 Azure DNS 目前不支持此服务。

我们正在 Azure DNS 积压工作中跟踪 URL 重定向功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)。

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS 是否支持适用于 TXT 记录集的扩展 ASCII 编码（8 位）集？

是的。 Azure DNS 支持适用于 TXT 记录集的扩展 ASCII 编码集。 但是，必须使用最新版本的 Azure REST API、SDK、PowerShell 和 CLI。 低于 2017 年 10 月 1 日版的版本或 SDK 2.1 不支持扩展的 ASCII 集。 

例如，你可能会提供一个字符串作为 TXT 记录的值，其中包含扩展的 ASCII 字符 \128。 例如“abcd\128efgh”。 Azure DNS 会在内部表示形式中使用此字符的字节值（即 128）。 在进行 DNS 解析时，此字节值会在响应中返回。 另请注意，在考虑到解析的情况下，“abc”和“\097\098\099”是可以互换的。 

我们遵循适用于 TXT 记录的 [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) 区域文件母版格式转义规则。 例如，按照 RFC 规则，`\` 现在实际上可以对所有内容进行转义操作。 如果指定 `A\B` 作为 TXT 记录值，则会在呈现时，仅将其解析为 `AB`。 如果确实需要让 TXT 记录在解析时呈现为 `A\B`，则需对 `\` 再次执行转义操作。 例如，指定 `A\\B`。

此支持目前不适用于通过 Azure 门户创建的 TXT 记录。

## <a name="alias-records"></a>别名记录

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>别名记录在哪些方案中有用？
请参阅 [Azure DNS 别名记录概述](dns-alias.md)中的方案部分。

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>别名记录集支持哪些记录类型？
在 Azure DNS 区域中，别名记录集支持以下记录类型：
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>支持哪些资源作为别名记录集的目标？

- **从 DNS A/AAAA 记录集指向公共 IP 资源**。 可以创建一个 A/AAAA 记录集，并使其成为指向公共 IP 资源的别名记录集。
- **从 DNS A/AAAA/CNAME 记录集指向流量管理器配置文件**。 可以从 DNS CNAME 记录集指向流量管理器配置文件的 CNAME。 例如 contoso.trafficmanager.net。 现在，还可以从 DNS 区域中的 A 或 AAAA 记录集指向包含外部终结点的流量管理器配置文件。
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，可以使 DNS CNAME 记录集成为相同类型的另一 CNAME 记录集的别名。 如果希望有些记录集是别名，有些记录集不是别名，则这种安排会很有用。

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>是否可以从 Azure 门户创建和更新别名记录？
是的。 除了 Azure REST API、PowerShell、CLI 和 SDK 以外，还可以在 Azure 门户中创建或管理别名记录。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>别名记录是否有助于确保在删除基础公共 IP 时，删除我的 DNS 记录集？
是的。 此功能是别名记录的一项核心功能。 它有助于避免应用程序用户遇到中断。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>当基础公共 IP 地址发生变化时，别名记录是否有助于确保我的 DNS 记录集更新为正确的 IP 地址？
是的。 此功能是别名记录的一项核心功能。 它有助于避免应用程序出现中断或安全风险。

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>使用 A 或 AAAA 记录的别名记录集指向流量管理器时，是否存在任何限制？
是的。 若要以别名形式从 A 或 AAAA 记录集指向流量管理器配置文件，流量管理器配置文件只能使用外部终结点。 在流量管理器中创建外部终结点时，请提供终结点的实际 IP 地址。

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>使用别名记录是否会产生额外的费用？
别名记录是对有效 DNS 记录集的限定。 别名记录不会产生额外的费用。

## <a name="use-azure-dns"></a>使用 Azure DNS

### <a name="can-i-cohost-a-domain-by-using-azure-dns-and-another-dns-provider"></a>是否可以使用 Azure DNS 和其他 DNS 提供程序共同托管域？

是的。 Azure DNS 支持与其他 DNS 服务共同托管域。

若要设置共同托管，请将域的 NS 记录修改为指向这两个提供程序的名称服务器。 名称服务器 (NS) 记录控制哪些提供程序接收域的 DNS 查询。 可在 Azure DNS、另一提供程序以及父区域中修改这些 NS 记录。 父区域通常是通过域名注册机构配置的。 有关 DNS 委派的详细信息，请参阅[DNS 域委派](dns-domain-delegation.md)。

此外，请确保域的 DNS 记录在 DNS 提供程序之间进行同步。 Azure DNS 目前不支持 DNS 区域传送。 必须使用 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlets](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)同步 DNS 记录。

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>是否需向全部四个 Azure DNS 名称服务器委托我的域？

是的。 Azure DNS 为每个 DNS 区域分配四个名称服务器。 这种安排是为实现故障隔离和提高复原能力。 为了符合 Azure DNS SLA，请将域委托给全部四个名称服务器。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS 有哪些使用限制？

使用 Azure DNS 时适用以下默认限制。

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>能否在资源组或订阅之间移动 Azure DNS 区域？

是的。 可在资源组或订阅之间移动 DNS 区域。

移动 DNS 区域不会影响 DNS 查询。 分配给区域的名称服务器将保持不变。 DNS 查询将以正常的吞吐量进行处理。

有关如何移动 DNS 区域的详细信息和说明，请参阅[将资源移动至新资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS 更改多久生效？

新的 DNS 区域和 DNS 记录通常很快就会显示在 Azure DNS 名称服务器中， 只需几秒钟。

对现有 DNS 记录的更改可能要在略长一段时间后才会显示。 它们通常会在 60 秒内显示在 Azure DNS 名称服务器中。 Azure DNS 外部的 DNS 客户端和 DNS 递归解析程序执行的 DNS 缓存也可能会影响显示时间。 若要控制此缓存持续时间，请使用每个记录集的生存时间 (TTL) 属性。

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>如何保护我的 DNS 区域不被意外删除？

可以使用 Azure 资源管理器管理 Azure DNS。 Azure DNS 受益于 Azure 资源管理器提供的访问控制功能。 基于角色的访问控制可以控制哪些用户具有对 DNS 区域和记录集的读取或写入访问权限。 资源锁可防止意外修改，或防止删除 DNS 区域和记录集。

有关详细信息，请参阅[保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>如何在 Azure DNS 中设置 SPF 记录？

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Azure DNS 名称服务器是否通过 IPv6 解析？ 

是的。 Azure DNS 名称服务器是双重堆栈。 双重堆栈表示它们具有 IPv4 和 IPv6 地址。 若要查找分配给 DNS 区域的 Azure DNS 名称服务器的 IPv6 地址，请使用 nslookup 等工具。 例如 `nslookup -q=aaaa <Azure DNS Nameserver>`。

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>如何在 Azure DNS 中设置 IDN？

国际域名 (IDN) 使用 [punycode](https://en.wikipedia.org/wiki/Punycode) 对每个 DNS 名称进行编码。 DNS 查询就是使用这些 punycode 编码名称构建的。

若要在 Azure DNS 中配置 IDN，请将区域名称或记录集名称转换为 punycode。 目前，Azure DNS 原生并不支持与 punycode 之间的相互转换。

## <a name="private-dns"></a>专用 DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支持专用域？

对专用域的支持是使用专用区域功能实现的。 此功能目前以公共预览版提供。 专用区域是使用与面向 Internet 的 Azure DNS 区域的相同工具管理的。 专用区域只能从指定的虚拟网络内部进行解析。 有关详细信息，请参阅[概述](private-dns-overview.md)。

目前，Azure 门户不支持专用区域。

有关 Azure 中其他内部 DNS 选项的信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>在专用区域的上下文中，注册虚拟网络与解析虚拟网络有什么区别？

可将虚拟网络作为注册虚拟网络或解析虚拟网络链接到 DNS 专用区域。 在任一情况下，虚拟网络中的虚拟机都可以根据专用区域中的记录成功解析。 使用注册虚拟网络时，DNS 记录将自动注册到虚拟网络中虚拟机所在的区域。 在删除注册虚拟网络中的虚拟机后，所链接的专用区域中的相应 DNS 记录会自动删除。 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 专用区域是否可跨 Azure 区域工作？

是的。 专用区域支持在跨 Azure 区域的虚拟网络之间进行 DNS 解析。 即使未显式建立虚拟网络对等互连，专用区域也能正常工作。 必须将所有虚拟网络指定为专用区域的解析虚拟网络。 客户可能需要建立虚拟网络的对等互连，才能在不同的区域之间传送 TCP/HTTP 流量。

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>专用区域是否需要在虚拟网络与 Internet 之间建立连接？

不是。 专用区域配合虚拟网络工作。 客户可在虚拟网络内部或跨虚拟网络管理虚拟机的域或其他资源。 无需建立 Internet 连接即可进行名称解析。 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>是否可将同一专用区域用于解析多个虚拟网络？

是的。 客户最多可将 10 个解析虚拟网络关联到一个专用区域。

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>是否可以在专用区域中，将属于不同订阅的虚拟网络添加为解析虚拟网络？

是的。 你必须对虚拟网络和专用 DNS 区域拥有写入操作权限。 可向多个 RBAC 角色授予“写入”权限。 例如，经典网络参与者 RBAC 角色对虚拟网络拥有写入权限。 有关 RBAC 角色的详细信息，请参阅[基于角色的访问控制](../role-based-access-control/overview.md)。

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>客户删除虚拟机后，是否会自动删除在专用区域中自动注册的虚拟机 DNS 记录？

是的。 如果删除注册虚拟网络中的虚拟机，则已注册到区域中的 DNS 记录会自动删除。 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>是否可以手动删除在注册虚拟网络的专用区域中自动注册的虚拟机记录？

不是。 从注册虚拟网络自动注册到专用区域中的虚拟机 DNS 记录不可见，也不可以由客户编辑。 可以在区域中使用手动创建的 DNS 记录来覆盖此类自动注册的 DNS 记录。 以下问答部分解答了此主题。

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>在主机名与注册虚拟网络中自动注册的现有虚拟机相同的专用区域中尝试手动创建新的 DNS 记录时，会发发生什么情况？

如果在主机名与注册虚拟网络中自动注册的现有虚拟机相同的专用区域中尝试手动创建新的 DNS 记录， 则新的 DNS 记录会覆盖自动注册的虚拟机记录。 如果再次尝试从区域中删除这条手动创建的 DNS 记录，则删除操作将会成功。 只要虚拟机仍然存在并且其上已附加专用 IP，就会再次发生自动注册。 DNS 记录将在区域中自动重新创建。

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>从专用区域中取消链接注册虚拟网络时会发生什么情况？ 虚拟网络中自动注册的虚拟机记录是否也会从区域中删除？

是的。 若要从某个专用区域中取消链接注册虚拟网络，请更新 DNS 区域以删除关联的注册虚拟网络。 在此过程中，自动注册的虚拟机记录将从区域中删除。 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>删除已链接到专用区域的注册或解析虚拟网络时，会发生什么情况？ 是否必须手动更新该专用区域，才能从该区域中取消链接用作注册或解析虚拟网络的虚拟网络？

是的。 删除注册或解析虚拟网络但事先未将它从专用区域中取消链接时，删除操作将会成功。 但是，虚拟网络不会从专用区域中自动取消链接（如果存在这种链接）。 必须手动从专用区域中取消链接虚拟网络。 出于此原因，请在删除虚拟网络之前，先从专用区域中取消其链接。

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>是否即使专用区域（例如 contoso.local）已链接到虚拟网络，也仍可使用默认 FQDN (internal.cloudapp.net) 进行 DNS 解析？

是的。 专用区域功能不能取代使用 Azure 提供的 internal.cloudapp.net 区域进行的默认 DNS 解析。 它只是一项补充性的功能或增强功能。 不管依赖于 Azure 提供的 internal.cloudapp.net 还是自己的专用区域，都请使用要解析的区域的 FQDN。 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>链接的虚拟网络中虚拟机上的 DNS 后缀是否会更改为专用区域的 DNS 后缀？

不是。 链接的虚拟网络中虚拟机上的 DNS 后缀将保留为 Azure 提供的默认后缀（“*.internal.cloudapp.net”）。 可以手动将虚拟机上的此 DNS 后缀更改为专用区域的 DNS 后缀。 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>专用区域预览版是否有任何限制？

是的。 公共预览版存在以下限制。
* 每个专用区域包含一个注册虚拟网络。
* 每个专用区域最多包含 10 个解析虚拟网络。
* 一个给定的虚拟网络只能作为注册虚拟网络链接到一个专用区域。
* 一个给定的虚拟网络可以作为解析虚拟网络链接到最多 10 个专用区域。
* 如果指定了注册虚拟网络，则无法通过 PowerShell、CLI 或 API 查看或检索注册到专用区域的虚拟网络中的 VM 的 DNS 记录。 VM 记录可成功注册和解析。
* 反向 DNS 仅适用于注册虚拟网络中的专用 IP 空间。
* 未在专用区域中注册的专用 IP的反向 DNS 将返回“internal.cloudapp.net”作为 DNS 后缀。 此后缀不可解析。 一个例子是作为解析虚拟网络链接到专用区域的虚拟网络中的虚拟机专用 IP。
* 当虚拟网络首次链接到专用区域作为注册或解析虚拟网络时，它必须为空。 以后作为注册或解析虚拟网络链接到其他专用区域时，虚拟网络可以是非空的。
* 例如，不支持使用条件转发来实现 Azure 与本地网络之间的解析。 了解客户如何通过其他机制实现此方案。 请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>专用区域的区域数或记录数是否存在任何配额或限制？

专用区域的每个订阅允许的区域数没有限制。 专用区域的每个区域的记录集数也没有限制。 公共和专用区域数将会计入总体 DNS 限制。 有关详细信息，请参阅 [Azure 订阅和服务限制](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>专用区域是否支持门户？

已通过 API、PowerShell、CLI 和 SDK 创建的专用区域将显示在 Azure 门户中。 但是，客户无法创建新的专用区域，或者管理与虚拟网络之间的关联。 对于作为注册虚拟网络关联的虚拟网络，门户中不会显示自动注册的 VM 记录。 

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure DNS](dns-overview.md)。
<br>
- [详细了解如何将 Azure DNS 用于专用域](private-dns-overview.md)。
<br>
- [详细了解 DNS 区域和记录](dns-zones-records.md)。
<br>
- [Azure DNS 入门](dns-getstarted-portal.md)。

