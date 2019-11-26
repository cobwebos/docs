---
title: 常见问题解答-Azure DNS
description: 本文详细介绍 Azure DNS 的常见问题
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: allensu
ms.openlocfilehash: c194d47842c927d3f8049a424dc08a34424a8a31
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212033"
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

DNS 区域用来托管某个特定域的 DNS 记录。 例如，域 contoso.com 可能包含几条 DNS 记录。 这些记录可能包含 mail.contoso.com（用于邮件服务器）和 www\.contoso.com（用于网站）。 这些记录托管在 DNS 区域 contoso.com 中。

域名仅是一个名称。 DNS 区域是包含域名的 DNS 记录的数据资源。 可以使用 Azure DNS 托管 DNS 区域，以及管理 Azure 中域的 DNS 记录。 它还提供 DNS 名称服务器，用于回答来自 Internet 的 DNS 查询。

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>是否需要购买 DNS 域名才能使用 Azure DNS？ 

不一定。

无需购买域即可托管 Azure DNS 中的 DNS 区域。 没有域名也可随时创建 DNS 区域。 仅当此区域的 DNS 查询定向到分配给该区域的 Azure DNS 名称服务器时，才会解析这些查询。

若要将 DNS 区域链接到全局 DNS 层级结构，则必须购买域名。 然后，来自全球任意位置的 DNS 查询会使用 DNS 记录来查找 DNS 区域和做出应答。

## <a name="azure-dns-features"></a>Azure DNS 功能

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>在流量管理器中使用域名顶点的别名记录时，是否存在任何限制？

可以。 必须在 Azure 流量管理器中使用静态公共 IP 地址。 使用静态 IP 地址配置**外部终结点**目标。 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS 是否支持基于 DNS 的流量路由或终结点故障转移？

流量管理器提供基于 DNS 的流量路由和终结点故障转移。 流量管理器是一项独立服务，可与 Azure DNS 配合使用。 有关详细信息，请参阅[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。

Azure DNS 仅支持托管静态 DNS 域，其中对某给定的 DNS 记录来说，每个 DNS 查询始终接收相同的 DNS 响应。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS 是否支持域名注册？

否。 Azure DNS 目前不支持购买域名。 若要购买域，必须使用第三方域名注册机构。 注册机构通常收取小额年费。 然后，可将域托管在 Azure DNS 中用于管理 DNS 记录。 有关详细信息，请参阅 [向 Azure DNS 委派域](dns-domain-delegation.md)。

我们正在 Azure 积压工作中跟踪域购买功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS 是否支持 DNSSEC？

否。 Azure DNS 目前不支持域名系统安全扩展 (DNSSEC)。

我们正在 Azure DNS 积压工作中跟踪 DNSSEC 功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS 是否支持区域传送 (AXFR/IXFR)？

否。 Azure DNS 目前不支持区域传送。 可[使用 Azure CLI 将 DNS 区域导入 Azure DNS](dns-import-export.md)。 然后，可通过 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/az.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)来托管 DNS 记录。

我们正在 Azure DNS 积压工作中跟踪区域传送功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS 是否支持 URL 重定向？

否。 URL 重定向服务实际并非 DNS 服务。 它们在 HTTP 级别而非 DNS 级别运行。 某些 DNS 提供商会在整体产品/服务中捆绑销售 URL 重定向服务。 Azure DNS 目前不支持此服务。

我们正在 Azure DNS 积压工作中跟踪 URL 重定向功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)。

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS 是否支持适用于 TXT 记录集的扩展 ASCII 编码（8 位）集？

可以。 Azure DNS 支持适用于 TXT 记录集的扩展 ASCII 编码集。 但是，必须使用最新版本的 Azure REST API、SDK、PowerShell 和 CLI。 低于 2017 年 10 月 1 日版的版本或 SDK 2.1 不支持扩展的 ASCII 集。 

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
- **指向 Azure 内容分发网络（CDN）终结点**。 当你使用 Azure 存储和 Azure CDN 创建静态网站时，这非常有用。
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，可以使 DNS CNAME 记录集成为相同类型的另一 CNAME 记录集的别名。 如果希望有些记录集是别名，有些记录集不是别名，则这种安排会很有用。

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>是否可以从 Azure 门户创建和更新别名记录？

可以。 除了 Azure REST API、PowerShell、CLI 和 SDK 以外，还可以在 Azure 门户中创建或管理别名记录。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>别名记录是否有助于确保在删除基础公共 IP 时，删除我的 DNS 记录集？

可以。 此功能是别名记录的一项核心功能。 它有助于避免应用程序用户遇到中断。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>当基础公共 IP 地址发生变化时，别名记录是否有助于确保我的 DNS 记录集更新为正确的 IP 地址？

可以。 此功能是别名记录的一项核心功能。 它有助于避免应用程序出现中断或安全风险。

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>使用 A 或 AAAA 记录的别名记录集指向流量管理器时，是否存在任何限制？

可以。 若要以别名形式从 A 或 AAAA 记录集指向流量管理器配置文件，流量管理器配置文件只能使用外部终结点。 在流量管理器中创建外部终结点时，请提供终结点的实际 IP 地址。

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>使用别名记录是否会产生额外的费用？

别名记录是对有效 DNS 记录集的限定。 别名记录不会产生额外的费用。

## <a name="use-azure-dns"></a>使用 Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>是否可以使用 Azure DNS 和其他 DNS 提供程序共同托管域？

可以。 Azure DNS 支持与其他 DNS 服务共同托管域。

若要设置共同托管，请将域的 NS 记录修改为指向这两个提供程序的名称服务器。 名称服务器 (NS) 记录控制哪些提供程序接收域的 DNS 查询。 可在 Azure DNS、另一提供程序以及父区域中修改这些 NS 记录。 父区域通常是通过域名注册机构配置的。 有关 DNS 委派的详细信息，请参阅[DNS 域委派](dns-domain-delegation.md)。

此外，请确保域的 DNS 记录在 DNS 提供程序之间进行同步。 Azure DNS 目前不支持 DNS 区域传送。 必须使用 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/az.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlets](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)同步 DNS 记录。

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>是否需向全部四个 Azure DNS 名称服务器委托我的域？

可以。 Azure DNS 为每个 DNS 区域分配四个名称服务器。 这种安排是为实现故障隔离和提高复原能力。 为了符合 Azure DNS SLA，请将域委托给全部四个名称服务器。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS 有哪些使用限制？

使用 Azure DNS 时适用以下默认限制。

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>能否在资源组或订阅之间移动 Azure DNS 区域？

可以。 可在资源组或订阅之间移动 DNS 区域。

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

可以。 Azure DNS 名称服务器是双重堆栈。 双重堆栈表示它们具有 IPv4 和 IPv6 地址。 若要查找分配给 DNS 区域的 Azure DNS 名称服务器的 IPv6 地址，请使用 nslookup 等工具。 例如 `nslookup -q=aaaa <Azure DNS Nameserver>`。

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>如何在 Azure DNS 中设置 IDN？

国际域名 (IDN) 使用 [punycode](https://en.wikipedia.org/wiki/Punycode) 对每个 DNS 名称进行编码。 DNS 查询就是使用这些 punycode 编码名称构建的。

若要在 Azure DNS 中配置 IDN，请将区域名称或记录集名称转换为 punycode。 目前，Azure DNS 原生并不支持与 punycode 之间的相互转换。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure DNS](dns-overview.md)。

- [详细了解如何将 Azure DNS 用于专用域](private-dns-overview.md)。

- [详细了解 DNS 区域和记录](dns-zones-records.md)。

- [Azure DNS 入门](dns-getstarted-portal.md)。
