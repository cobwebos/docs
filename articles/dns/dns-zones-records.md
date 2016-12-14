---
title: "DNS 区域和记录 | Microsoft Docs"
description: "对在 Microsoft Azure DNS 中托管 DNS 区域和记录的支持的概述。"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: jtuliani
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: b99bc20b66787c7a87fa49143b4cbf861e0e68a2

---

# <a name="dns-zones-and-records"></a>DNS 区域和记录

此页说明域、DNS 区域和 DNS 记录和记录集的关键概念以及它们在 Azure DNS 中的受支持方式。

## <a name="domain-names"></a>域名

域名系统是域的层次结构。 该层次结构从名为“**.**”的“根”域开始。  根域的下面是顶级域，例如“com”、“net”、“org”、“uk”或“jp”。  再往下是二级域，例如“org.uk”或“co.jp”。 DNS 层次结构中的域遍布全球，由世界各地的 DNS 名称服务器托管。

域名注册机构是一个组织，可以通过该组织购买域名，例如“contoso.com”。  购买域名便有权控制该域名下的 DNS 层次结构，例如可将名称“www.contoso.com”定向到公司网站。 注册机构会代表用户在域自身的名称服务器中托管域，或者用户可以指定可选名称服务器。

Azure DNS 提供全球分布的高可用性名称服务器基础结构，可将其用于托管域。 通过在 Azure DNS 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具、计费和支持来管理 DNS 记录。

Azure DNS 当前不支持购买域名。 如果想要购买域名，需要使用第三方域名注册机构。 注册机构通常将收取小额年费。 然后，域可以托管在 Azure DNS 中以管理 DNS 记录。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

## <a name="dns-zones"></a>DNS 区域

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后将在此 DNS 区域内为每个 DNS 记录创建域。

例如，域“contoso.com”可能包含几条 DNS 记录，如“mail.contoso.com”（用于邮件服务器）和“www.contoso.com”（用于网站）。

在 Azure DNS 中创建 DNS 区域时，区域名称在资源组中必须唯一。 可在不同资源组或不同 Azure 订阅中重复使用同一区域名称。 当多个区域共享相同的名称时，将为每个实例分配不同的名称服务器地址。 使用域名注册机构仅可配置一组地址。

> [!NOTE]
> 不必拥有域名即可在 Azure DNS 中以该域名创建 DNS 区域。 但是，需要拥有域才能通过域名注册机构将 Azure DNS 名称服务器配置为域名的正确名称服务器。

有关详细信息，请参阅 [向 Azure DNS 委派域](dns-domain-delegation.md)。

## <a name="dns-records"></a>DNS 记录

### <a name="record-types"></a>记录类型

每个 DNS 记录都有一个名称和类型。 这些记录根据其所包含的数据分为各种类型。 最常见的类型为“A”记录，这种记录将名称映射到 IPv4 地址。 另一种常见类型是“MX”记录，这种记录将名称映射到邮件服务器。

Azure DNS 支持所有常见 DNS 记录类型：A、AAAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT。

### <a name="record-names"></a>记录名称

在 Azure DNS 中，记录使用相对名称指定。 完全限定的域名 (FQDN) 包括区域名称，而相对域名则不包括。 例如，“contoso.com”区域中的相对记录名称“www”会提供完全限定的记录名称“www.contoso.com”。

顶点记录是位于 DNS 区域的根（或顶点）中的 DNS 记录。 例如，在 DNS 区域“contoso.com”中，顶点记录还具有完全限定的名称“contoso.com”（有时称为裸域）。  按照惯例，相对名称 '@' 用于创建顶点记录。

### <a name="record-sets"></a>记录集

有时，需要创建具有给定名称和类型的多个 DNS 记录。 例如，假设在两个不同的 IP 地址上托管“www.contoso.com”网站。 该网站需要两个不同的 A 记录，每个 IP 地址一个。 这就是记录集：

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS 使用记录集管理 DNS 记录。 记录集（也称为资源记录集）是某个区域中具有相同名称、相同类型的 DNS 记录的集合。 大多数记录集都包含单个记录，但像这样的包含多个记录的记录集也不少见。

例如，假设已在区域“contoso.com”中创建 A 记录“www”，指向 IP 地址“134.170.185.46”（上述第一条记录）。  若要创建第二条记录，应将此记录添加到现有记录集而非创建新记录集。

SOA 和 CNAME 记录类型例外。 对于这些类型，DNS 标准不允许多个记录具有相同的名称，因此这些记录集仅可包含单个记录。

### <a name="time-to-live"></a>生存时间

生存时间（或 TTL）指定客户端在重新查询之前缓存每个记录的时长。 在上例中，TTL 为 3600 秒或 1 小时。

在 Azure DNS 中，TTL 针对记录集而非每个记录指定，因此同一个值适用于该记录集中的所有记录。  可以指定介于 1 和 2,147,483,647 秒之间的任何 TTL 值。

### <a name="wildcard-records"></a>通配符记录

Azure DNS 支持 [通配符记录](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。 具有匹配名称的任何查询都会返回通配符记录（除非存在与非通配符记录集更接近的匹配项）。 除 NS 和 SOA 外的所有记录类型都支持通配符记录集。

若要创建通配符记录集，请使用记录集名称“\*”。 或者，还可以使用将“\*”作为最左边的标签的名称，例如，“\*.foo”。

### <a name="cname-records"></a>CNAME 记录

CNAME 记录集不能与其他具有相同名称的记录集共存。 例如，不能同时创建具有相对名称“www”的 CNAME 记录集和具有相对名称“www”的 A 记录。

由于区域顶点（名称 = '@')）始终包含创建区域时创建的 NS 和 SOA 记录集，因此不能在区域顶点创建 CNAME 记录集。

这些约束起源于 DNS 标准，并非 Azure DNS 的限制。

### <a name="ns-records"></a>NS 记录

NS 记录集在每个区域（名称 = '@'),）的顶点处自动创建，并在删除该区域时自动删除（不能单独删除）。  用户可以修改此记录集的 TTL，但无法修改记录，该记录预配置为引用分配给该区域的 Azure DNS 名称服务器。

可以创建和删除该区域内（不在区域顶点处）的其他 NS 记录。  通过此操作可配置子区域（请参阅[在 Azure DNS 中委托子域](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)。）

### <a name="soa-records"></a>SOA 记录

SOA 记录集在每个区域（名称 = '@'),）的顶点处自动创建，并在删除该区域时自动删除。  无法单独创建或删除 SOA 记录。

用户可以修改 SOA 记录的所有属性，但“主机”属性除外，此属性预配置为引用 Azure DNS 所提供的主名称服务器名。

### <a name="spf-records"></a>SPF 记录

发送方策略框架 (SPF) 记录用于指定允许代表给定域名发送电子邮件的电子邮件服务器。  正确配置 SPF 记录非常重要，可防止收件人将你的邮件标记为“垃圾邮件”。

DNS RFC 最初引入了新的“SPF”记录类型来支持这种方案。 为了支持旧名称服务器，还允许使用 TXT 记录类型指定 SPF 记录。  这种不明确性导致混乱，已通过 [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) 得到解决。  其中规定：应仅使用 TXT 记录类型创建 SPF 记录，并弃用 SPF 记录类型。

**SPF 记录受 Azure DNS 支持且应使用 TXT 记录类型创建。** 不支持已过时的 SPF 记录类型。 [导入 DNS 区域文件](dns-import-export.md)时，使用 SPF 记录类型的任何 SPF 记录将转换为 TXT 记录类型。

### <a name="srv-records"></a>SRV 记录

多种服务使用 [SRV 记录](https://en.wikipedia.org/wiki/SRV_record)指定服务器位置。 在 Azure DNS 中指定 SRV 记录时：

* 服务和协议 必须指定为前面带下划线的记录集名称的一部分。  例如，“\_sip.\_tcp.name”。  对于区域顶点处的记录，无需在记录名称中指定 '@'，只需使用服务和协议，例如“\_sip.\_tcp”。
* 将 priority、weight、port 和 target 指定为记录集中每个记录的参数。

## <a name="tags-and-metadata"></a>标记和元数据

### <a name="tags"></a>标记

标记是名称/值列表，Azure Resource Manager 利用它们来标记资源。  Azure Resource Manager 使用标记来启用 Azure 帐单的筛选视图，并支持设置需要标记的策略。 有关标记的详细信息，请参阅 [使用标记来组织 Azure 资源](../resource-group-using-tags.md)。

Azure DNS 支持使用 DNS 区域资源上的 Azure Resource Manager 标记。  它不支持 DNS 记录集上的标记。

### <a name="metadata"></a>Metadata

作为记录集标记的替代方法，Azure DNS 支持使用“元数据”批注记录集。  与标记相类似，通过元数据可将名称/值对与每个记录集相关联。  这非常有用，例如可用于记录每个记录集的用途。  与标记不同的是，元数据不能用于提供 Azure 帐单的筛选视图，且不能在 Azure Resource Manager 策略中指定。

## <a name="limits"></a>限制

使用 Azure DNS 时，以下默认限制适用：

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>后续步骤

* 若要开始使用 Azure DNS，请了解如何[创建 DNS 区域](dns-getstarted-create-dnszone-portal.md)和[创建 DNS 记录](dns-getstarted-create-recordset-portal.md)。
* 若要迁移现有 DNS 区域，请了解如何[导入 DNS 区域文件](dns-import-export.md)。




<!--HONumber=Nov16_HO3-->


