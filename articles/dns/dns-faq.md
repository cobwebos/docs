---
title: "Azure DNS 常见问题解答 | Microsoft 文档"
description: "有关 Azure DNS 的常见问题"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f365574a12047f6952209dc3883af32a2e9ecd1e
ms.contentlocale: zh-cn
ms.lasthandoff: 04/22/2017

---

# <a name="azure-dns-faq"></a>Azure DNS 常见问题解答

## <a name="about-azure-dns"></a>关于 Azure DNS

### <a name="what-is-azure-dns"></a>什么是 Azure DNS？

域名系统或 DNS 负责将网站或服务名称转换（或解析）为它的 IP 地址。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管你的域，你可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理你的 DNS 记录。

Azure DNS 中的 DNS 域托管在 DNS 名称服务器的 Azure 全球网络上。 我们使用任意广播网络，以便每个 DNS 查询由最近的可用 DNS 服务器来应答。 这为你的域提供更快的性能和高可用性。

Azure DNS 服务基于 Azure Resource Manager。 因此，它可以利用资源管理器功能，例如基于角色的访问控制、审核日志和资源锁定。 可以通过 Azure 门户、Azure PowerShell cmdlet 和跨平台 Azure CLI 对域和记录进行管理。 需要自动 DNS 管理的应用程序可通过 REST API 和 SDK 与服务集成。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 的费用是多少？

Azure DNS 计费模型基于 Azure DNS 中托管的 DNS 区域数并按其接收的 DNS 查询数进行计费。 根据使用情况提供折扣。

有关详细详细，请参阅 [Azure DNS 定价页](https://azure.microsoft.com/pricing/details/dns/)。

### <a name="what-is-the-sla-for-azure-dns"></a>什么是 SLA for Azure DNS？

我们保证在至少 99.99% 的时间内，有效的 DNS 请求将从至少一个 Azure DNS 名称服务器中收到响应。

有关详细详细，请参阅 [Azure DNS SLA 页](https://azure.microsoft.com/support/legal/sla/dns)。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>什么是“DNS 区域”？ 它是否等同于 DNS 域？ 

域在域名系统中具有唯一名称，例如“contoso.com”。

DNS 区域用来托管某个特定域的 DNS 记录。 例如，域“contoso.com”可能包含几条 DNS 记录，如“mail.contoso.com”（用于邮件服务器）和“www.contoso.com”（用于网站）。 它们会托管在 DNS 区域“contoso.com” 中。

域名仅是一个名称，但 DNS 区域是包含域名的 DNS 记录的数据资源。 使用 Azure DNS 可以托管 DNS 区域，以及管理 Azure 中域的 DNS 记录。 它还提供 DNS 名称服务器，用于回答来自 Internet 的 DNS 查询。

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>我是否需要购买 DNS 域名才能使用 Azure DNS？ 

不一定。

你无需购买域即可托管 Azure DNS 中的 DNS 区域。 没有域名也可随时创建 DNS 区域。 仅当此区域的 DNS 查询定向到分配给该区域的 Azure DNS 名称服务器时，才会解析这些查询。

需购买域名才可将 DNS 区域链接到全局 DNS 层级结构，利用此结构可从全球各地查询 DNS 以查找 DNS 区域和找到相应的 DNS 记录。

## <a name="azure-dns-features"></a>Azure DNS 功能

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS 是否支持基于 DNS 的流量路由或终结点故障转移？

Azure 流量管理器提供基于 DNS 的流量路由和终结点故障转移。 该 Azure 服务是一项独立服务，可与 Azure DNS 配合使用。 有关详细信息，请参阅[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。

Azure DNS 仅支持托管“静态”DNS 域，其中对某给定的 DNS 记录来说，每个 DNS 查询始终接收相同的 DNS 响应。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS 是否支持域名注册？

否。 Azure DNS 当前不支持购买域名。 如果想要购买域，则需要使用第三方域名注册机构。 注册机构通常收取小额年费。 然后，域可以托管在 Azure DNS 中以管理 DNS 记录。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

我们正在待办列表中跟踪这项功能。 可使用反馈站点[记录你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)。

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支持“私有”域？

否。 Azure DNS 当前仅支持面向 Internet 的域。

我们正在待办列表中跟踪这项功能。 可使用反馈站点[记录你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/10737696-enable-split-dns-for-providing-both-public-and-int)。

若要了解 Azure 中的内部 DNS 选项，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS 是否支持 DNSSEC？

否。 Azure DNS 当前不支持 DNSSEC。

我们正在待办列表中跟踪这项功能。 可使用反馈站点[记录你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS 是否支持区域传送 (AXFR/IXFR)？

否。 Azure DNS 当前不支持区域传送。 可[使用 Azure CLI 将 DNS 区域导入 Azure DNS](dns-import-export.md)。 然后，可通过 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlets](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)来托管 DNS 记录。

我们正在待办列表中跟踪这项功能。 可使用反馈站点[记录你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS 是否支持 URL 重定向？

否。 URL 重定向服务实际并非 DNS 服务 - 它们在 HTTP 级别运行，而非 DNS 级别。 某些 DNS 提供商会在整体产品/服务中捆绑销售 URL 重定向服务。 Azure DNS 目前不支持此服务。

我们正在待办列表中跟踪此功能。 可使用反馈站点[记录你对此功能的支持](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)。

## <a name="using-azure-dns"></a>使用 Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>我能否使用 Azure DNS 和其他 DNS 提供程序共同托管域？

是的。 Azure DNS 支持与其他 DNS 服务共同托管域。

为此，需修改域的 NS 记录（其控制哪些提供程序接收域的 DNS 查询），以便指向全部两个提供程序的名称服务器。 需在三处修改这些 NS 记录：Azure DNS 中、另一提供程序中，以及父区域中（通常经由域名注册机构配置）。 有关 DNS 委派的详细信息，请参阅[DNS 域委派](dns-domain-delegation.md)。

此外，还需确保域的 DNS 记录在 DNS 提供程序之间进行同步。 Azure DNS 目前不支持 DNS 区域传送。 需通过 [Azure DNS 管理门户](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell cmdlets](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)同步 DNS 记录。

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>是否需向全部 4 个 Azure DNS 名称服务器委托我的域？

是的。 Azure DNS 为每个 DNS 区域分配 4 个名称服务器，用于故障隔离和增强恢复能力。 要符合 Azure DNS SLA 的使用资格，需将域委托给全部 4 个名称服务器。

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

Azure DNS 由 Azure Resource Manager 托管，且受益于 Azure Resource Manager 的访问控制功能。 通过基于角色的访问控制，可控制哪些用户具有对 DNS 区域和记录集的读取权限或写权限。 可使用资源锁防止意外修改，或防止删除 DNS 区域和记录集。

有关详细信息，请参阅[保护 DNS 区域和记录](dns-protect-zones-recordsets.md)。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>如何在 Azure DNS 中设置 SPF 记录？

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>如何在 Azure DNS 中设置国际域名 (IDN)？

国际域名 (IDN) 的工作原理是使用 [punycode](https://en.wikipedia.org/wiki/Punycode) 进行每个 DNS 名称的编码。 punycode 编码的这些名称用于查询 DNS。

可在 Azure DNS 中配置国际域名 (IDN)，方式是先将区域名称或记录集名称转换为 punycode。 Azure DNS 目前不支持内置转换至 punycode 或从中进行转换。

## <a name="next-steps"></a>后续步骤

[详细了解 Azure DNS](dns-overview.md)
<br>
[详细了解 DNS 区域和记录](dns-zones-records.md)
<br>
[开始使用 Azure DNS](dns-getstarted-portal.md)


