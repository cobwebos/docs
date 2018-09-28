---
title: Azure DNS 别名记录概述
description: 对 Microsoft Azure DNS 中别名记录的支持的概述。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957543"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 别名记录概述

Azure DNS 别名记录是关于 DNS 记录集的限定，使你能够引用 DNS 区域内的其他 Azure 资源。 例如，可以创建别名记录集，它引用 Azure 公共 IP 地址，而不是 A 记录。 由于别名记录集动态指向 Azure 公共 IP 地址服务实例，因此别名记录集在 DNS 解析过程中可自行无缝更新。

在 Azure DNS 区域中，别名记录集支持以下记录类型：A、AAAA 和 CNAME。 

> [!NOTE]
> 流量管理器的 A 或 AAAA 记录类型的别名记录仅支持外部终结点类型。 必须根据需要，为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址（理想情况下为静态 IP）。

## <a name="capabilities"></a>功能

- **从 DNS A/AAAA 记录集指向公共 IP 资源**。 可以创建一个 A/AAAA 记录集，并使其成为指向公共 IP 资源的别名记录集。
- **从 DNS A/AAAA/CNAME 记录集指向流量管理器配置文件**。 除了能够从 DNS CNAME 记录集指向流量管理器配置文件的 CNAME（例如：contoso.trafficmanager.net）外，现在还可以从 DNS 区域中的 A 或 AAAA 记录集指向具有外部终结点的流量管理器配置文件。
   > [!NOTE]
   > 流量管理器的 A 或 AAAA 记录类型的别名记录仅支持外部终结点类型。 必须根据需要，为流量管理器中的外部终结点提供 IPv4 或 IPv6 地址（理想情况下为静态 IP）。
- **指向同一区域中的另一 DNS 记录集**。 别名记录可引用相同类型的其他记录集。 例如，可以使 DNS CNAME 记录集成为相同类型的另一 CNAME 记录集的别名。 如果要使部分记录集成为别名，部分成为有关行为的非别名，这将有所帮助。

## <a name="scenarios"></a>方案
下面是几个别名记录的常见方案：

### <a name="prevent-dangling-dns-records"></a>防止无关联的 DNS 记录
在 Azure DNS 区域中，别名记录可用于密切跟踪 Azure 资源的生命周期（例如公共 IP 和流量管理器配置文件）。 传统 DNS 记录的常见问题之一是“无关联记录”，尤其是 A/AAAA 或 CNAME 记录类型。 

对于传统的 DNS 区域记录，如果目标 IP 或 CNAME 不再存在，则 DNS 区域记录不会了解该事实，且需要手动进行更新。 在某些组织中，此手动更新可能无法及时进行，或可能由于角色和关联的权限级别分离而出现问题。

例如，有权删除属于应用程序的 CNAME 或 IP 地址的角色可能没有足够的权限来更新指向这些目标的 DNS 记录。 因此，删除 IP 或 CNAME，并且删除指向它的 DNS 记录时可能存在时间延迟，这可能导致最终用户服务中断。

别名记录消除了与此方案相关的复杂性，并有助于防止此类无关联引用。 当 DNS 记录限定为别名记录，以指向公共 IP 或流量管理器配置文件时，如果删除这些基础资源，同时也会删除 DNS 别名记录。 这可确保最终用户永远不会遇到服务中断的情况。

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>应用程序 IP 更改时，DNS 区域自动更新

类似于前面的方案，如果移动应用程序或重启基础虚拟机，当 IP 地址针对基础公共 IP 资源发生更改时，别名记录将自动更新。 如果将最终用户定向到具有旧 IP 地址的其他应用程序，则可避免潜在的安全风险。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>在区域顶点托管负载均衡应用程序

DNS 协议可防止分配区域顶点的 A 或 AAAA 记录之外的任何内容（例如：contoso.com）。 这将为流量管理器背后拥有负载均衡应用程序的应用程序所有者带来问题，因为不可能从区域顶点记录指向流量管理器配置文件。 因此，应用程序所有者被迫使用一种解决方法。 例如，应用程序层的重定向，从区域顶点重定向到其他域（从 contoso.com 到 www.contoso.com）。 这将呈现重定向功能方面的单一故障点。

使用别名记录，此问题将不再存在。 应用程序所有者现在可以将其区域顶点记录指向具有外部终结点的流量管理器配置文件。 借助此功能，应用程序所有者可以指向用于其 DNS 区域中任何其他域的相同流量管理器配置文件。 例如，只要流量管理器配置文件仅配置了外部终结点，那么 contoso.com 和 www.contoso.com 可同时指向同一流量管理器配置文件，。

## <a name="next-steps"></a>后续步骤

若要了解有关别名记录的详细信息，请参阅以下文章：

- [教程：配置表示 Azure 公共 IP 地址的别名记录](tutorial-alias-pip.md)
- [教程：使用流量管理器支持顶点域名的别名记录](tutorial-alias-tm.md)
- [DNS 常见问题](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
