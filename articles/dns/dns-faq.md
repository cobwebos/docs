---
title: Azure DNS 常见问题解答 | Microsoft 文档
description: 有关 Azure DNS 的常见问题
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: f07f914ccf8ea6df216e3f571e38d7628b2d7fb6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="azure-dns-faq"></a>Azure DNS 常见问题解答

## <a name="about-azure-dns"></a>关于 Azure DNS

### <a name="what-is-azure-dns"></a>什么是 Azure DNS？

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全球网络上。 Azure DNS 使用任意广播网络，以便每个 DNS 查询由最近的可用 DNS 服务器来应答。 Azure DNS 为域提供更快的性能和高可用性。

Azure DNS 服务基于 Azure 资源管理器。 因此，它可以利用 Resource Manager 功能，例如基于角色的访问控制、审核日志和资源锁定。 可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 对域和记录进行管理。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务集成。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 的费用是多少？

Azure DNS 计费模型基于 Azure DNS 中托管的 DNS 区域数并按其接收的 DNS 查询数进行计费。 根据使用情况提供折扣。

有关详细详细，请参阅 [Azure DNS 定价页](https://azure.microsoft.com/pricing/details/dns/)。

### <a name="what-is-the-sla-for-azure-dns"></a>什么是 SLA for Azure DNS？

Azure 保证在至少 99.99% 的时间内，有效的 DNS 请求将从至少一个 Azure DNS 名称服务器中收到响应。

有关详细详细，请参阅 [Azure DNS SLA 页](https://azure.microsoft.com/support/legal/sla/dns)。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>什么是“DNS 区域”？ 它是否等同于 DNS 域？ 

域在域名系统中具有唯一名称，例如“contoso.com”。

DNS 区域用来托管某个特定域的 DNS 记录。 例如，域“contoso.com”可能包含几条 DNS 记录，如“mail.contoso.com”（用于邮件服务器）和“www.contoso.com”（用于网站）。 这些记录托管在 DNS 区域“contoso.com” 中。

域名仅是一个名称，但 DNS 区域是包含域名的 DNS 记录的数据资源。 使用 Azure DNS 可以托管 DNS 区域，以及管理 Azure 中域的 DNS 记录。 它还提供 DNS 名称服务器，用于回答来自 Internet 的 DNS 查询。

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>我是否需要购买 DNS 域名才能使用 Azure DNS？ 

不一定。

无需购买域即可托管 Azure DNS 中的 DNS 区域。 没有域名也可随时创建 DNS 区域。 仅当此区域的 DNS 查询定向到分配给该区域的 Azure DNS 名称服务器时，才会解析这些查询。

需购买域名才可将 DNS 区域链接到全局 DNS 层级结构，利用此链接可从全球各地查询 DNS 以查找 DNS 区域和找到相应的 DNS 记录。

## <a name="azure-dns-features"></a>Azure DNS 功能

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS 是否支持基于 DNS 的流量路由或终结点故障转移？

Azure 流量管理器提供基于 DNS 的流量路由和终结点故障转移。 Azure 流量管理器是一项独立服务，可与 Azure DNS 配合使用。 有关详细信息，请参阅[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。

Azure DNS 仅支持托管“静态”DNS 域，其中对某给定的 DNS 记录来说，每个 DNS 查询始终接收相同的 DNS 响应。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS 是否支持域名注册？

不会。 Azure DNS 当前不支持购买域名。 如果想要购买域，则需要使用第三方域名注册机构。 注册机构通常收取小额年费。 然后，域可以托管在 Azure DNS 中以管理 DNS 记录。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

我们正在 Azure 积压工作中跟踪域购买功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS 是否支持 DNSSEC？

不会。 Azure DNS 当前不支持 DNSSEC。

我们正在 Azure DNS 积压工作中跟踪 DNSSEC 功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS 是否支持区域传送 (AXFR/IXFR)？

不会。 Azure DNS 当前不支持区域传送。 可[使用 Azure CLI 将 DNS 区域导入 Azure DNS](dns-import-export.md)。 然后，可通过 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)来托管 DNS 记录。

我们正在 Azure DNS 积压工作中跟踪区域传输功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS 是否支持 URL 重定向？

不会。 URL 重定向服务实际并非 DNS 服务 - 它们在 HTTP 级别运行，而非 DNS 级别。 某些 DNS 提供商会在整体产品/服务中捆绑销售 URL 重定向服务。 Azure DNS 目前不支持此服务。

我们正在 Azure DNS 积压工作中跟踪 URL 重定向功能。 可以使用反馈站点来[表示你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)。

## <a name="using-azure-dns"></a>使用 Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>我能否使用 Azure DNS 和其他 DNS 提供程序共同托管域？

是的。 Azure DNS 支持与其他 DNS 服务共同托管域。

为此，需修改域的 NS 记录（其控制哪些提供程序接收域的 DNS 查询），以便指向全部两个提供程序的名称服务器。 可在三个位置修改这些 NS 记录：Azure DNS 中、另一提供程序中，以及父区域中（通常经由域名注册机构配置）。 有关 DNS 委派的详细信息，请参阅[DNS 域委派](dns-domain-delegation.md)。

此外，还需确保域的 DNS 记录在 DNS 提供程序之间进行同步。 Azure DNS 目前不支持 DNS 区域传送。 需通过 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlets](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)同步 DNS 记录。

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>是否需向全部四个 Azure DNS 名称服务器委托我的域？

是的。 Azure DNS 为每个 DNS 区域分配四个名称服务器，用于故障隔离和增强恢复能力。 要符合 Azure DNS SLA 的使用资格，需将域委托给全部四个名称服务器。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS 有哪些使用限制？

使用 Azure DNS 时，以下默认限制适用：

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>能否在资源组或订阅之间移动 Azure DNS 区域？

是的。 可在资源组或订阅之间移动 DNS 区域。

移动 DNS 区域时不会影响 DNS 查询。 分配给区域的名称服务器将保持不变，DNS 查询将始终正常处理。

有关如何移动 DNS 区域的详细信息和说明，请参阅[将资源移动至新资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS 更改多久生效？

新的 DNS 区域和 DNS 记录通常很快（几秒钟内）即会反映到 Azure DNS 名称服务器上。

现有 DNS 记录的更改可能耗时长一点，但仍会在 60 秒内反映到 Azure DNS 名称服务器上。 此情况下，Azure DNS 外部的 DNS 缓存（通过 DNS 客户端和 DNS 递归解析程序）也可能影响 DNS 更改的生效时间。 可使用每个记录集的生存时间 (TTL) 属性控制此缓存的持续时间。

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>如何保护我的 DNS 区域不被意外删除？

Azure DNS 由 Azure 资源管理器管理，且受益于 Azure 资源管理器的访问控制功能。 通过基于角色的访问控制，可控制哪些用户具有对 DNS 区域和记录集的读取权限或写权限。 可使用资源锁防止意外修改，或防止删除 DNS 区域和记录集。

有关详细信息，请参阅[保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>如何在 Azure DNS 中设置 SPF 记录？

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Azure DNS 名称服务器是否通过 IPv6 解析？ 

是的。 Azure DNS 名称服务器是双堆栈（同时具有 IPv4 和 IPv6 地址）。 若要查找分配给 DNS 区域的 Azure DNS 名称服务器的 IPv6 地址，可以使用 nslookup 等工具（例如，`nslookup -q=aaaa <Azure DNS Nameserver>`）。

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>如何在 Azure DNS 中设置国际域名 (IDN)？

国际域名 (IDN) 的工作原理是使用 [punycode](https://en.wikipedia.org/wiki/Punycode) 进行每个 DNS 名称的编码。 punycode 编码的这些名称用于查询 DNS。

可在 Azure DNS 中配置国际域名 (IDN)，方式是先将区域名称或记录集名称转换为 punycode。 Azure DNS 目前不支持内置转换至 punycode 或从中进行转换。

## <a name="private-dns"></a>专用 DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支持“私有”域？
对“专用”域的支持是使用专用区域功能实现的。  此功能目前以公共预览版提供。  专用区域与面向 Internet 的 Azure DNS 区域使用相同的工具托管，但它们只能从指定的虚拟网络中解析。  有关详细信息，请参阅[概述](private-dns-overview.md)。

目前，Azure 门户不支持专用区域。 

有关 Azure 中其他内部 DNS 选项的信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>在专用区域的上下文中，注册虚拟网络与解析虚拟网络有什么区别？ 
可通过两种方式将虚拟网络链接到 DNS 专用区域 - 作为注册虚拟网络，或者作为解析虚拟网络。 在任一情况下，虚拟网络中的虚拟机都可以根据专用区域中的记录成功解析。 但是，如果将某个虚拟网络指定为注册虚拟网络，则 Azure 会将 DNS 记录自动注册（动态注册）到虚拟网络中虚拟机所在的区域。 此外，在删除注册虚拟网络中的虚拟机后，Azure 还会从所链接的专用区域中删除对应的 DNS 记录。 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 专用区域是否可跨 Azure 区域工作？
是的。 专用区域支持在跨 Azure 区域的虚拟网络之间进行 DNS 解析，即使未显式建立虚拟网络对等互连也是如此，但前提是将所有虚拟网络指定为专用区域的解析虚拟网络。 客户可能需要建立虚拟网络的对等互连，才能在不同的区域之间传送 TCP/HTTP 流量。 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>专用区域是否需要在虚拟网络与 Internet 之间建立连接？
不会。 专用区域配合虚拟网络工作，可让客户在虚拟网络内部或跨虚拟网络管理虚拟机的域或其他资源。 无需建立 Internet 连接即可进行名称解析。 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>是否可将同一专用区域用于解析多个虚拟网络？ 
是的。 客户最多可将 10 个解析虚拟网络关联到一个专用区域。

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>是否可以在专用区域中，将属于不同订阅的虚拟网络添加为解析虚拟网络？ 
可以，前提是用户对虚拟网络以及专用 DNS 区域拥有“写入”操作权限。 请注意，“写入”权限可能已分配到多个 RBAC 角色。 例如，经典网络参与者 RBAC 角色对虚拟网络拥有写入权限。 有关 RBAC 角色的详细信息，请阅读[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)。

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>客户删除虚拟机后，是否会自动删除在专用区域中自动注册的虚拟机 DNS 记录？
是的。 如果删除注册虚拟网络中的虚拟机，我们会自动删除已注册到区域中的 DNS 记录，因为这是一个注册虚拟网络。 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>是否可以手动删除专用区域中自动注册的虚拟机记录（通过注册虚拟网络）？ 
不会。 目前，从注册虚拟网络自动注册到专用区域中的虚拟机 DNS 记录不可见，也不可以由客户编辑。 但是，可以在区域中，使用手动创建的 DNS 记录来替换（覆盖）此类自动注册的 DNS 记录。 请参阅以下问题及其解答。

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>在主机名与注册虚拟网络中（自动注册的）现有虚拟机相同的专用区域中尝试手动创建新的 DNS 记录时，会发发生什么情况？ 
如果在主机名与注册虚拟网络中（自动注册的）现有虚拟机相同的专用区域中尝试手动创建新的 DNS 记录，我们允许新的 DNS 记录覆盖自动注册的虚拟机记录。 此外，如果随后尝试从区域中删除这条手动创建的 DNS 记录，则删除操作将会成功，并且会再次发生自动注册（在区域中自动重新创建 DNS 记录），前提是虚拟机仍然存在并且其上已附加专用 IP。 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>从专用区域中取消链接注册虚拟网络时会发生什么情况？ 该虚拟网络中自动注册的虚拟机记录是否也会从该区域中删除？
是的。 如果从某个专用区域中取消链接注册虚拟网络（更新 DNS 区域以删除关联的注册虚拟网络），则 Azure 会从该区域中删除所有自动注册的虚拟机记录。 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>删除已链接到专用区域的注册（或解析）虚拟网络时，会发生什么情况？ 是否必须手动更新该专用区域，才能从该区域中取消链接用作注册（或解析）虚拟网络的虚拟网络？
是的。 删除注册（或解析）虚拟网络但事先未将它从专用区域中取消链接时，Azure 可让删除操作成功，但虚拟网络不会从专用区域中自动取消链接（如果存在这种链接）。 需要手动从专用区域中取消链接虚拟网络。 出于此原因，我们建议在删除虚拟网络之前，先从专用区域中取消其链接。

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>是否即使专用区域（例如：contoso.local）已链接到虚拟网络，也仍可使用默认 FQDN (internal.cloudapp.net) 进行 DNS 解析？ 
是的。 专用区域功能不能取代使用 Azure 提供的 internal.cloudapp.net 区域进行的默认 DNS 解析，而只能用作补充性的功能或增强功能。 对于这两种情况（不管依赖于 Azure 提供的 internal.cloudapp.net 还是自己的专用区域），我们都建议使用要解析的区域的 FQDN。 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>链接的虚拟网络中虚拟机上的 DNS 后缀是否会更改为专用区域的 DNS 后缀？ 
不会。 目前，链接的虚拟网络中虚拟机上的 DNS 后缀将保留为 Azure 提供的默认后缀（“*.internal.cloudapp.net”）。 但是，可以手动将虚拟机上的此 DNS 后缀更改为专用区域的 DNS 后缀。 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>专用区域预览版是否有任何限制？
是的。 公共预览版存在以下限制：
* 每个专用区域只能包含 1 个注册虚拟网络
* 每个专用区域最多包含 10 个解析虚拟网络
* 一个给定的虚拟网络只能作为注册虚拟网络链接到一个专用区域
* 一个给定的虚拟网络可以作为解析虚拟网络链接到最多 10 个专用区域
* 如果指定了注册虚拟网络，则无法通过 Powershell/CLI/API 查看或检索注册到专用区域的虚拟网络中的 VM 的 DNS 记录，但是，VM 记录确实已注册并且会成功解析
* 反向 DNS 仅适用于注册虚拟网络中的专用 IP 空间
* 未注册到专用区域中的专用 IP（例如，作为解析虚拟网络链接到专用区域的虚拟网络中的虚拟机专用 IP）的反向 DNS 将返回“internal.cloudapp.net”作为 DNS 后缀，但此后缀不可解析。   
* 在最初（即首次）作为 注册或解析虚拟网络链接到 专用区域的虚拟网络需是空的（即，所有虚拟网络上都没有附加 NIC）。 但是，以后作为注册或解析虚拟网络链接到其他专用区域时，虚拟网络可以是非空的。 
* 目前不支持条件转发，例如，为了启用 Azure 与本地网络之间的解析。 有关客户可以如何通过其他机制实现此方案的文档，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>专用区域的区域数或记录数是否存在任何配额或限制？
专用区域中每个订阅允许的区域数或每个区域允许的记录集数没有单独的限制。 公共和专用区域数将会计入[此处](../azure-subscription-service-limits.md#dns-limits)所述的总体 DNS 限制

### <a name="is-there-portal-support-for-private-zones"></a>专用区域是否支持门户？
通过非门户机制 (API/PowerShell/CLI/SDK) 创建的专用区域将显示在 Azure 门户中，但客户无法创建新的专用区域，或者管理与虚拟网络之间的关联。 此外，对于作为注册虚拟网络关联的虚拟网络，门户中不会显示自动注册的 VM 记录。 

## <a name="next-steps"></a>后续步骤

[详细了解 Azure DNS](dns-overview.md)
<br>
[详细了解如何将 Azure DNS 用于专用域](private-dns-overview.md)
<br>
[详细了解 DNS 区域和记录](dns-zones-records.md)
<br>
[开始使用 Azure DNS](dns-getstarted-portal.md)

