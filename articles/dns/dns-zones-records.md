---
title: "DNS 区域和记录概述 - Azure DNS | Microsoft Docs"
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
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: jonatul
ms.openlocfilehash: 5818986c939c464a364c52ab31225e15130ab30e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-dns-zones-and-records"></a>DNS 区域和记录概述

此页说明域、DNS 区域和 DNS 记录和记录集的关键概念以及它们在 Azure DNS 中的受支持方式。

## <a name="domain-names"></a>域名

域名系统是域的层次结构。 该层次结构从名为“**.**”的“根”域开始。  根域的下面是顶级域，例如“com”、“net”、“org”、“uk”或“jp”。  再往下是二级域，例如“org.uk”或“co.jp”。 DNS 层次结构中的域遍布全球，由世界各地的 DNS 名称服务器托管。

域名注册机构是一个组织，可以通过该组织购买域名，例如“contoso.com”。  购买域名便有权控制该域名下的 DNS 层次结构，例如可将名称“www.contoso.com”定向到公司网站。 注册机构会代表用户在域自身的名称服务器中托管域，或者允许用户指定可选名称服务器。

Azure DNS 提供全球分布的高可用性名称服务器基础结构，可将其用于托管域。 通过在 Azure DNS 中托管域，用户可以使用与其他 Azure 服务相同的凭据、API、工具、计费和支持来管理 DNS 记录。

Azure DNS 当前不支持购买域名。 如果想要购买域名，需要使用第三方域名注册机构。 注册机构通常收取小额年费。 然后，域可以托管在 Azure DNS 中以管理 DNS 记录。 有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

## <a name="dns-zones"></a>DNS 区域

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS 记录

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>生存时间

生存时间（或 TTL）指定客户端在重新查询之前缓存每个记录的时长。 在上例中，TTL 为 3600 秒或 1 小时。

在 Azure DNS 中，TTL 针对记录集而非每个记录指定，因此同一个值适用于该记录集中的所有记录。  可以指定介于 1 和 2,147,483,647 秒之间的任何 TTL 值。

### <a name="wildcard-records"></a>通配符记录

Azure DNS 支持 [通配符记录](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。 具有匹配名称的任何查询都会返回通配符记录（除非存在与非通配符记录集更接近的匹配项）。 对于除 NS 和 SOA 外的所有记录类型，Azure DNS 都支持通配符记录集。

若要创建通配符记录集，请使用记录集名称“\*”。 或者，还可以使用将“\*”作为最左边的标签的名称，例如，“\*.foo”。

### <a name="cname-records"></a>CNAME 记录

CNAME 记录集不能与其他具有相同名称的记录集共存。 例如，不能同时创建具有相对名称“www”的 CNAME 记录集和具有相对名称“www”的 A 记录。

由于区域顶点（名称 =“@”）始终包含创建区域时创建的 NS 和 SOA 记录集，因此不能在区域顶点创建 CNAME 记录集。

这些约束起源于 DNS 标准，并非 Azure DNS 的限制。

### <a name="ns-records"></a>NS 记录

区域顶点（名称“@”）处的 NS 记录集随每个 DNS 区域自动创建，并在删除该区域时自动删除（不能单独删除）。

此记录集包含分配给该区域的 Azure DNS 名称服务器名称。 可向此 NS 记录集添加其他名称服务器，从而支持与多个 DNS 提供商共同托管域。 还可修改此记录集的 TTL 和元数据。 但是，无法删除或修改预填充的 Azure DNS 名称服务器。 

请注意，这仅适用于区域顶点处的 NS 记录集。 区域中的其他 NS 记录集（用于委派子区域）不受约束，可进行创建、修改和删除。

### <a name="soa-records"></a>SOA 记录

SOA 记录集在每个区域（名称 =“@”）的顶点处自动创建，并在删除该区域时自动删除。  无法单独创建或删除 SOA 记录。

用户可以修改 SOA 记录的所有属性，但“主机”属性除外，此属性预配置为引用 Azure DNS 所提供的主名称服务器名。

### <a name="spf-records"></a>SPF 记录

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV 记录

多种服务使用 [SRV 记录](https://en.wikipedia.org/wiki/SRV_record)指定服务器位置。 在 Azure DNS 中指定 SRV 记录时：

* 服务和协议 必须指定为前面带下划线的记录集名称的一部分。  例如，“\_sip.\_tcp.name”。  对于区域顶点处的记录，无需在记录名称中指定“@”，只需使用服务和协议，例如“\_sip.\_tcp”。
* 将 priority、weight、port 和 target 指定为记录集中每个记录的参数。

### <a name="txt-records"></a>TXT 记录

TXT 记录用于将域名映射到任意文本字符串。 它们在多个应用程序中使用，特别是与电子邮件配置相关（如[发件人策略框架 (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework)和[域密钥识别邮件 (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)）。

DNS 标准允许单个 TXT 记录包含多个字符串，其中每个字符串的长度可以最多为 254 个字符。 使用多个字符串时，它们由客户端连接在一起，被视为单个字符串。

调用 Azure DNS REST API 时，需要单独指定每个 TXT 字符串。  使用 Azure 门户、PowerShell 或 CLI 接口时，应对每个记录指定单个字符串（这会在需要时自动划分为 254 个字符的段）。

DNS 记录中的多个字符串不应与 TXT 记录集的多个 TXT 记录混淆。  TXT 记录集可以包含多个记录，其中每个可以包含多个字符串。  Azure DNS 在每个 TXT 记录集（跨所有合并的记录）中支持总长度最多 1024 个字符。

## <a name="tags-and-metadata"></a>标记和元数据

### <a name="tags"></a>标记

标记是名称/值列表，Azure Resource Manager 利用它们来标记资源。  Azure Resource Manager 使用标记来启用 Azure 帐单的筛选视图，并支持设置需要标记的策略。 有关标记的详细信息，请参阅 [使用标记来组织 Azure 资源](../azure-resource-manager/resource-group-using-tags.md)。

Azure DNS 支持使用 DNS 区域资源上的 Azure Resource Manager 标记。  它不支持 DNS 记录集的标记，不过作为替代方法，在 DNS 记录集上支持“元数据”，如下所述。

### <a name="metadata"></a>元数据

作为记录集标记的替代方法，Azure DNS 支持使用“元数据”批注记录集。  与标记相类似，通过元数据可将名称/值对与每个记录集相关联。  这非常有用，例如可用于记录每个记录集的用途。  与标记不同的是，元数据不能用于提供 Azure 帐单的筛选视图，且不能在 Azure Resource Manager 策略中指定。

## <a name="etags"></a>Etag

假设两个人或两个进程尝试同时修改一条 DNS 记录。 哪一个占先？ 占先方是否知道他们/它们覆盖了其他人/进程创建的更改？

Azure DNS 使用 Etag 来安全地处理对同一资源的并发更改。 Etag 与 [Azure Resource Manager “标记”](#tags)不同。 每个 DNS 资源（区域或记录集）都有与其相关联的 Etag。 只要检索资源，就会检索其 Etag。 当更新资源时，可以选择传递回 Etag 的选项以便 Azure DNS 可以验证服务器上的 Etag 是否匹配。 由于对资源的每次更新都会导致重新生成 Etag，Etag 不匹配表示发生了并发更改。 当创建新的资源时也可以使用 Etag，以确保该资源不存在。

默认情况下，Azure DNS PowerShell 使用 Etag 来阻止对区域和记录集的并发更改。 可选 -Overwrite 开关可用于取消 Etag 检查，这种情况下会覆盖发生的所有并发更改。

Etag 是在 Azure DNS REST API 级别使用 HTTP 标头指定的。  下表给出了它们的行为：

| 标头 | 行为 |
| --- | --- |
| 无 |PUT 始终成功（没有 Etag 检查） |
| If-match <etag> |只有当资源存在并且 Etag 匹配时，PUT 才会成功 |
| If-match * |只有当资源存在时，PUT 才会成功 |
| If-none-match * |只有当资源不存在时，PUT 才会成功 |


## <a name="limits"></a>限制

使用 Azure DNS 时，以下默认限制适用：

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>后续步骤

* 若要开始使用 Azure DNS，请了解如何[创建 DNS 区域](dns-getstarted-create-dnszone-portal.md)和[创建 DNS 记录](dns-getstarted-create-recordset-portal.md)。
* 若要迁移现有 DNS 区域，请了解如何[导入和导出 DNS 区域文件](dns-import-export.md)。
