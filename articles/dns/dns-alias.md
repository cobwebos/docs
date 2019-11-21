---
title: Alias records overview - Azure DNS
description: In this article, learn about support for alias records in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: allensu
ms.openlocfilehash: da396d3e3db4acd1a9843977b8b3e3d51c33f021
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212327"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 别名记录概述

Azure DNS 别名记录是对 DNS 记录集的限定。 它们可以引用 DNS 区域中的其他 Azure 资源。 例如，可以创建引用 Azure 公共 IP 地址而不是 A 记录的别名记录集。 别名记录集动态指向 Azure 公共 IP 地址服务实例。 因此，别名记录集在 DNS 解析过程中可自行无缝更新。

Azure DNS 区域中的以下记录类型支持别名记录集： 

- A
- AAAA
- CNAME

> [!NOTE]
> 如果你打算使用 A 或 AAAA 记录类型的别名记录来指向 [Azure 流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)，则必须确保流量管理器配置文件仅具有[外部终结点](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)。 必须为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址。 You can't use fully-qualified domain names (FQDNs) in endpoints. 最好是使用静态 IP 地址。

## <a name="capabilities"></a>功能

- **从 DNS A/AAAA 记录集指向公共 IP 资源**。 You can create an A/AAAA record set and make it an alias record set to point to a public IP resource (standard or basic). The DNS record set changes automatically if the public IP address changes or is deleted. 这可以避免出现指向不正确 IP 地址的无关联 DNS 记录。

   There is a current limit of 20 alias records sets per resource.

- **从 DNS A/AAAA/CNAME 记录集指向流量管理器配置文件**。 你可以创建 A/AAAA 或 CNAME 记录集并使用别名记录来将其指向流量管理器配置文件。 It's especially useful when you need to route traffic at a zone apex, as traditional CNAME records aren't supported for a zone apex. 例如，假设流量管理器配置文件为 myprofile.trafficmanager.net 并且业务 DNS 区域为 contoso.com， 则可以为 contoso.com（区域顶点）创建一个 A/AAAA 类型的别名记录集，并使其指向 myprofile.trafficmanager.net。
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，DNS CNAME 记录集可以是另一个 CNAME 记录集的别名。 如果希望有些记录集是别名，有些记录集不是别名，则这种安排会很有用。

## <a name="scenarios"></a>方案

下面是别名记录的几种常见方案。

### <a name="prevent-dangling-dns-records"></a>防止无关联的 DNS 记录

传统 DNS 记录的常见问题之一是“无关联记录”。 For example, DNS records that haven't been updated to reflect changes to IP addresses. A/AAAA 或 CNAME 记录类型特别容易出现此问题。

对于传统的 DNS 区域记录，如果目标 IP 或 CNAME 不再存在，则必须手动更新与之关联的 DNS 记录。 In some organizations, a manual update might not happen in time because of process issues or the separation of roles and associated permission levels. 例如，某个角色可能有权删除属于应用程序的 CNAME 或 IP 地址， 但它没有足够的权限来更新指向这些目标的 DNS 记录。 更新 DNS 记录时的延迟可能会导致对用户造成服务中断。

别名记录通过将 DNS 记录的生命周期与 Azure 资源紧密耦合来防止出现无关联引用。 例如，假设某个 DNS 记录限定为别名记录，以指向公共 IP 地址或流量管理器配置文件。 If you delete those underlying resources, the DNS alias record becomes an empty record set. It no longer references the deleted resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>当应用程序 IP 地址更改时自动更新 DNS 记录集

此方案类似于前面所述的方案。 也许应用程序已移动，或基础虚拟机已重启。 当基础公共 IP 资源的 IP 地址发生更改时，别名记录将自动更新。 这避免了将用户定向到分配有旧的公共 IP 地址的另一个应用程序，从而避免了潜在的安全风险。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>在区域顶点托管负载均衡应用程序

DNS 协议会阻止在区域顶点分配 CNAME 记录。 For example if your domain is contoso.com; you can create CNAME records for somelabel.contoso.com; but you can't create CNAME for contoso.com itself.
对于在 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)后面具有负载均衡应用程序的应用程序所有者，此限制会带来问题。 Since using a Traffic Manager profile requires creation of a CNAME record, it isn't possible to point at the Traffic Manager profile from the zone apex.

This problem is solved using alias records. Unlike CNAME records, alias records are created at the zone apex and application owners can use it to point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. 若要详细了解如何将别名记录与 Azure 流量管理器配置文件配合使用，请参阅“后续步骤”部分。

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point zone apex to Azure CDN endpoints

Just like a Traffic Manager profile, you can also use alias records to point your DNS zone apex to Azure CDN endpoints. This is useful when you create static websites using Azure storage and Azure CDN. You can then access the website without prepending "www" to your DNS name.

For example, if your static website is named www.contoso.com, your users can access your site using contoso.com without the need to prepend www to the DNS name.

As described previously, CNAME records aren't supported at the zone apex. So, you can’t use a CNAME record to point contoso.com to your CDN endpoint. Instead, you can use an alias record to point the zone apex to a CDN endpoint directly.

> [!NOTE]
> Pointing a zone apex to CDN endpoints for Azure CDN from Akamai is currently not supported.

## <a name="next-steps"></a>后续步骤

若要详细了解别名记录，请参阅以下文章：

- [教程：配置引用 Azure 公共 IP 地址的别名记录](tutorial-alias-pip.md)
- [教程：使用流量管理器支持顶点域名的别名记录](tutorial-alias-tm.md)
- [DNS 常见问题](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
