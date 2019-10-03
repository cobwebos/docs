---
title: Azure DNS 别名记录概述
description: 对 Microsoft Azure DNS 中别名记录的支持的概述。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: victorh
ms.openlocfilehash: 9a3cdb846921c2d73dd2cca5d679663c1ba9e192
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946898"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 别名记录概述

Azure DNS 别名记录是对 DNS 记录集的限定。 它们可以引用 DNS 区域中的其他 Azure 资源。 例如，可以创建引用 Azure 公共 IP 地址而不是 A 记录的别名记录集。 别名记录集动态指向 Azure 公共 IP 地址服务实例。 因此，别名记录集在 DNS 解析过程中可自行无缝更新。

Azure DNS 区域中的以下记录类型支持别名记录集： 

- A
- AAAA
- CNAME

> [!NOTE]
> 如果你打算使用 A 或 AAAA 记录类型的别名记录来指向 [Azure 流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)，则必须确保流量管理器配置文件仅具有[外部终结点](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)。 必须为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址。 不能在终结点中使用完全限定的域名 (Fqdn)。 最好是使用静态 IP 地址。

## <a name="capabilities"></a>功能

- **从 DNS A/AAAA 记录集指向公共 IP 资源**。 你可以创建/AAAA 记录集, 并将其设置为指向公共 IP 资源 (标准或基本) 的别名记录集。 如果公共 IP 地址更改或被删除, 则 DNS 记录集会自动更改。 这可以避免出现指向不正确 IP 地址的无关联 DNS 记录。

   每个资源当前限制为20个别名记录集。

- **从 DNS A/AAAA/CNAME 记录集指向流量管理器配置文件**。 你可以创建 A/AAAA 或 CNAME 记录集并使用别名记录来将其指向流量管理器配置文件。 当你需要在区域顶点上路由流量时, 此方法特别有用, 因为区域顶点不支持传统的 CNAME 记录。 例如，假设流量管理器配置文件为 myprofile.trafficmanager.net 并且业务 DNS 区域为 contoso.com， 则可以为 contoso.com（区域顶点）创建一个 A/AAAA 类型的别名记录集，并使其指向 myprofile.trafficmanager.net。
- **指向 Azure 内容分发网络 (CDN) 终结点**。 当你使用 Azure 存储和 Azure CDN 创建静态网站时, 这非常有用。
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，DNS CNAME 记录集可以是另一个 CNAME 记录集的别名。 如果希望有些记录集是别名，有些记录集不是别名，则这种安排会很有用。

## <a name="scenarios"></a>方案

下面是别名记录的几种常见方案。

### <a name="prevent-dangling-dns-records"></a>防止无关联的 DNS 记录

传统 DNS 记录的常见问题之一是“无关联记录”。 例如, 尚未更新的 DNS 记录反映对 IP 地址的更改。 A/AAAA 或 CNAME 记录类型特别容易出现此问题。

对于传统的 DNS 区域记录，如果目标 IP 或 CNAME 不再存在，则必须手动更新与之关联的 DNS 记录。 在某些组织中, 手动更新可能不会发生, 因为进程问题或角色与关联的权限级别分离。 例如，某个角色可能有权删除属于应用程序的 CNAME 或 IP 地址， 但它没有足够的权限来更新指向这些目标的 DNS 记录。 更新 DNS 记录时的延迟可能会导致对用户造成服务中断。

别名记录通过将 DNS 记录的生命周期与 Azure 资源紧密耦合来防止出现无关联引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 如果删除这些基础资源, DNS 别名记录将变成空的记录集。 它不再引用已删除的资源。

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>当应用程序 IP 地址更改时自动更新 DNS 记录集

此方案类似于前面所述的方案。 也许应用程序已移动，或基础虚拟机已重启。 当基础公共 IP 资源的 IP 地址发生更改时，别名记录将自动更新。 这避免了将用户定向到分配有旧的公共 IP 地址的另一个应用程序，从而避免了潜在的安全风险。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>在区域顶点托管负载均衡应用程序

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如, 如果域为 contoso.com, 则为;您可以为 somelabel.contoso.com 创建 CNAME 记录;但不能为 contoso.com 本身创建 CNAME。
对于在 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)后面具有负载均衡应用程序的应用程序所有者，此限制会带来问题。 由于使用流量管理器配置文件需要创建 CNAME 记录, 因此无法从区域顶点指向流量管理器配置文件。

使用别名记录可解决此问题。 与 CNAME 记录不同, 别名记录是在区域顶点创建的, 应用程序所有者可以将其区域顶点记录指向具有外部终结点的流量管理器配置文件。 应用程序所有者指向用于其 DNS 区域中的任何其他域的同一流量管理器配置文件。

例如, contoso.com 和 www\.contoso.com 可以指向同一流量管理器配置文件。 若要详细了解如何将别名记录与 Azure 流量管理器配置文件配合使用，请参阅“后续步骤”部分。

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Azure CDN 终结点的点区顶点

与流量管理器配置文件一样, 你还可以使用别名记录将 DNS 区域顶点到 Azure CDN 终结点。 当你使用 Azure 存储和 Azure CDN 创建静态网站时, 这非常有用。 然后, 你可以访问该网站, 而无需为你的 DNS 名称预先计算 "www"。

例如, 如果你的静态网站命名为 www.contoso.com, 则你的用户可以使用 contoso.com 访问你的网站, 而无需在 DNS 名称前面预置 www。

如前文所述, 区域顶点不支持 CNAME 记录。 因此, 不能使用 CNAME 记录将 contoso.com 指向 CDN 终结点。 相反, 你可以使用别名记录将区域顶点直接指向 CDN 终结点。

> [!NOTE]
> 当前不支持从 Akamai 指向 Azure CDN 的 CDN 终结点的区域顶点。

## <a name="next-steps"></a>后续步骤

若要详细了解别名记录，请参阅以下文章：

- [教程：配置表示 Azure 公共 IP 地址的别名记录](tutorial-alias-pip.md)
- [教程：配置别名记录来支持为流量管理器使用顶点域名](tutorial-alias-tm.md)
- [DNS 常见问题](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
