---
title: 别名记录概述 - Azure DNS
description: 在本文中，了解对 Microsoft Azure DNS 中的别名记录的支持。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295490"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 别名记录概述

Azure DNS 别名记录是对 DNS 记录集的限定。 它们可以引用 DNS 区域中的其他 Azure 资源。 例如，可以创建引用 Azure 公共 IP 地址而不是 A 记录的别名记录集。 别名记录集动态指向 Azure 公共 IP 地址服务实例。 因此，别名记录集在 DNS 解析过程中可自行无缝更新。

Azure DNS 区域中的以下记录类型支持别名记录集： 

- A
- AAAA
- CNAME

> [!NOTE]
> 如果你打算使用 A 或 AAAA 记录类型的别名记录来指向 [Azure 流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)，则必须确保流量管理器配置文件仅具有[外部终结点](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)。 必须为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址。 不能在端点中使用完全限定的域名 （FQDN）。 最好是使用静态 IP 地址。

## <a name="capabilities"></a>功能

- **从 DNS A/AAAA 记录集指向公共 IP 资源**。 您可以创建 A/AAAA 记录集，并将其设置为别名记录集以指向公共 IP 资源（标准或基本）。 如果公共 IP 地址发生更改或删除，DNS 记录集将自动更改。 这可以避免出现指向不正确 IP 地址的无关联 DNS 记录。

   每个资源当前限制为 20 个别名记录集。

- **从 DNS A/AAAA/CNAME 记录集中指向流量管理器配置文件。** 你可以创建 A/AAAA 或 CNAME 记录集并使用别名记录来将其指向流量管理器配置文件。 当您需要在区域顶点路由流量时，它特别有用，因为区域顶点不支持传统的 CNAME 记录。 例如，假设流量管理器配置文件为 myprofile.trafficmanager.net 并且业务 DNS 区域为 contoso.com， 则可以为 contoso.com（区域顶点）创建一个 A/AAAA 类型的别名记录集，并使其指向 myprofile.trafficmanager.net。
- **指向 Azure 内容传递网络 （CDN） 终结点**。 当您使用 Azure 存储和 Azure CDN 创建静态网站时，这非常有用。
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，DNS CNAME 记录集可以是另一个 CNAME 记录集的别名。 如果希望有些记录集是别名，有些记录集不是别名，则这种安排会很有用。

## <a name="scenarios"></a>方案

下面是别名记录的几种常见方案。

### <a name="prevent-dangling-dns-records"></a>防止无关联的 DNS 记录

传统 DNS 记录的常见问题之一是“无关联记录”。 例如，尚未更新以反映对 IP 地址的更改的 DNS 记录。 A/AAAA 或 CNAME 记录类型特别容易出现此问题。

对于传统的 DNS 区域记录，如果目标 IP 或 CNAME 不再存在，则必须手动更新与之关联的 DNS 记录。 在某些组织中，由于流程问题或角色和相关权限级别的分离，手动更新可能无法及时进行。 例如，某个角色可能有权删除属于应用程序的 CNAME 或 IP 地址， 但它没有足够的权限来更新指向这些目标的 DNS 记录。 更新 DNS 记录时的延迟可能会导致对用户造成服务中断。

别名记录通过将 DNS 记录的生命周期与 Azure 资源紧密耦合来防止出现无关联引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 如果删除这些基础资源，DNS 别名记录将成为空记录集。 它不再引用已删除的资源。

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>当应用程序 IP 地址更改时自动更新 DNS 记录集

此方案类似于前面所述的方案。 也许应用程序已移动，或基础虚拟机已重启。 当基础公共 IP 资源的 IP 地址发生更改时，别名记录将自动更新。 这避免了将用户定向到分配有旧的公共 IP 地址的另一个应用程序，从而避免了潜在的安全风险。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>在区域顶点托管负载均衡应用程序

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果您的域是contoso.com;例如，您的域是contoso.com您可以为somelabel.contoso.com创建 CNAME 记录;但是，您无法为contoso.com本身创建 CNAME。
对于在 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)后面具有负载均衡应用程序的应用程序所有者，此限制会带来问题。 由于使用流量管理器配置文件需要创建 CNAME 记录，因此无法从区域顶点指向流量管理器配置文件。

此问题已使用别名记录解决。 与 CNAME 记录不同，别名记录是在区域顶点创建，应用程序所有者可以使用它将区域顶点记录指向具有外部终结点的流量管理器配置文件。 应用程序所有者指向用于其 DNS 区域内任何其他域的相同流量管理器配置文件。

例如，contoso.com和 www\.contoso.com 可以指向相同的流量管理器配置文件。 若要详细了解如何将别名记录与 Azure 流量管理器配置文件配合使用，请参阅“后续步骤”部分。

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>指向 Azure CDN 终结点的点区域顶点

与流量管理器配置文件一样，您还可以使用别名记录将 DNS 区域顶点指向 Azure CDN 终结点。 当您使用 Azure 存储和 Azure CDN 创建静态网站时，这非常有用。 然后，您可以访问网站，而无需预先挂起"www"到您的 DNS 名称。

例如，如果静态网站被命名`www.contoso.com, your users can access your site using contoso.com`，而无需将 www 预置为 DNS 名称。

如前所述，区域顶点不支持 CNAME 记录。 因此，不能使用 CNAME 记录将contoso.com指向 CDN 终结点。 相反，您可以使用别名记录将区域顶点直接指向 CDN 终结点。

> [!NOTE]
> 当前不支持将来自 Akamai 的 Azure CDN 的区域顶点指向 CDN 终结点。

## <a name="next-steps"></a>后续步骤

若要详细了解别名记录，请参阅以下文章：

- [教程：配置别名记录以引用 Azure 公共 IP 地址](tutorial-alias-pip.md)
- [教程：使用流量管理器支持顶点域名的别名记录](tutorial-alias-tm.md)
- [DNS 常见问题解答](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
