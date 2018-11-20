---
title: Azure DNS 别名记录概述
description: 对 Microsoft Azure DNS 中别名记录的支持的概述。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092836"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 别名记录概述

Azure DNS 别名记录是对 DNS 记录集的限定。 它们可以引用 DNS 区域中的其他 Azure 资源。 例如，可以创建引用 Azure 公共 IP 地址而不是 A 记录的别名记录集。 别名记录集动态指向 Azure 公共 IP 地址服务实例。 因此，别名记录集在 DNS 解析过程中可自行无缝更新。

Azure DNS 区域中的以下记录类型支持别名记录集： 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> 只有外部终结点类型支持 Azure 流量管理器的 A 或 AAAA 记录类型的别名记录。 必须根据需要，为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址。 最好是对地址使用静态 IP。

## <a name="capabilities"></a>功能

- **从 DNS A/AAAA 记录集指向公共 IP 资源**。 可以创建一个 A/AAAA 记录集，并使其成为指向公共 IP 资源的别名记录集。

- **从 DNS A/AAAA/CNAME 记录集指向流量管理器配置文件**。 可以从 DNS CNAME 记录集指向流量管理器配置文件的 CNAME。 例如 contoso.trafficmanager.net。 现在，还可以从 DNS 区域中的 A 或 AAAA 记录集指向包含外部终结点的流量管理器配置文件。

   > [!NOTE]
   > 只有外部终结点类型支持流量管理器的 A 或 AAAA 记录类型的别名记录。 必须根据需要，为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址。 最好是对地址使用静态 IP。
   
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，可以使 DNS CNAME 记录集成为相同类型的另一 CNAME 记录集的别名。 如果希望有些记录集是别名，有些记录集不是别名，则这种安排会很有用。

## <a name="scenarios"></a>方案
下面是别名记录的几种常见方案。

### <a name="prevent-dangling-dns-records"></a>防止无关联的 DNS 记录
 在 Azure DNS 区域中，别名记录可用于密切跟踪 Azure 资源的生命周期。 资源包括公共 IP 或流量管理器配置文件。 传统 DNS 记录的常见问题之一是“无关联记录”。 A/AAAA 或 CNAME 记录类型特别容易出现此问题。 

对于传统的 DNS 区域记录，如果目标 IP 或 CNAME 不再存在，则 DNS 区域记录不会了解该事实。 因此，必须手动更新记录。 在某些组织中，这种手动更新可能无法及时进行。 另外，角色和关联权限级别的分离也可能导致问题。

例如，某个角色可能有权删除属于应用程序的 CNAME 或 IP 地址， 但它没有足够的权限来更新指向这些目标的 DNS 记录。 删除 IP 或 CNAME 的时间与删除指向它的 DNS 记录的时间存在延迟。 这种时间延迟可能给用户造成服务中断。

别名记录消除了与此方案相关的复杂性。 它们有助于防止无关联的引用。 例如，如果某个 DNS 记录限定为别名记录，以指向公共 IP 或流量管理器配置文件。 如果删除这些基础资源，则同时也会删除 DNS 别名记录。 此过程可确保用户永远不会遇到服务中断。

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>应用程序 IP 更改时，DNS 区域自动更新

此方案类似于前面所述的方案。 也许应用程序已移动，或基础虚拟机已重启。 当基础公共 IP 资源的 IP 地址发生更改时，别名记录将自动更新。 为了避免潜在的安全风险，可将用户定向到具有旧 IP 地址的另一应用程序。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>在区域顶点托管负载均衡应用程序

DNS 协议可防止分配区域顶点的 A 或 AAAA 记录之外的任何内容。 例如 contoso.com。 这种限制将为流量管理器背后拥有负载均衡应用程序的应用程序所有者带来问题。 无法从区域顶点记录指向流量管理器配置文件。 因此，应用程序所有者必须使用一种解决方法。 应用程序层的重定向必须从区域顶点重定向到另一个域。 例如，从 contoso.com 重定向到 www.contoso.com。 这种方案会给重定向功能带来单一故障点。

使用别名记录，此问题将不再存在。 应用程序所有者现在可将其区域顶点记录指向具有外部终结点的流量管理器配置文件。 应用程序所有者可以指向用于其 DNS 区域中任何其他域的相同流量管理器配置文件。 例如，contoso.com 和 www.contoso.com 可以指向同一流量管理器配置文件。 只要流量管理器配置文件仅配置了外部终结点，就会出现这种情况。

## <a name="next-steps"></a>后续步骤

若要详细了解别名记录，请参阅以下文章：

- [教程：配置引用 Azure 公共 IP 地址的别名记录](tutorial-alias-pip.md)
- [教程：使用流量管理器支持顶点域名的别名记录](tutorial-alias-tm.md)
- [DNS 常见问题](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
