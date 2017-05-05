---
title: "Azure DNS 委托概述 | Microsoft Docs"
description: "了解如何更改域委托并使用 Azure DNS 名称服务器提供域托管。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: ab6dd0e1e5975770bec741ed4a06b6eb4745e174
ms.lasthandoff: 04/21/2017

---

# <a name="delegation-of-dns-zones-with-azure-dns"></a>使用 Azure DNS 委托 DNS 区域

使用 Azure DNS 可以托管 DNS 区域，以及管理 Azure 中域的 DNS 记录。 要使域的 DNS 查询才能抵达 Azure DNS，必须将该域从其父域委托给 Azure DNS。 请记住，Azure DNS 不是域注册机构。 本文介绍域委托的工作原理，以及如何将域委托给 Azure DNS。

## <a name="how-dns-delegation-works"></a>DNS 委托的工作原理

### <a name="domains-and-zones"></a>域和区域

域名系统是域的层次结构。 该层次结构从名为“**.**”的“根”域开始。  根域的下面是顶级域，例如“com”、“net”、“org”、“uk”或“jp”。  这些顶级域的下面是二级域，例如“org.uk”或“co.jp”。  依此类推。 DNS 层次结构中的域托管在不同的 DNS 区域。 这些区域遍布全球，由世界各地的 DNS 名称服务器托管。

**DNS 区域** - 域在域名系统中具有一个唯一名称，例如“contoso.com”。 DNS 区域用来托管某个特定域的 DNS 记录。 例如，域“contoso.com”可能包含几条 DNS 记录，如“mail.contoso.com”（用于邮件服务器）和“www.contoso.com”（用于网站）。

**域注册机构** - 域注册机构是可以提供 Internet 域名的公司。 它们将会验证所要使用的 Internet 域是否可用，以及是否允许购买。 注册域名后，注册者即是该域名的合法所有者。 如果已有一个 Internet 域，将使用当前的域注册机构将该域委托给 Azure DNS。

若要详细了解谁拥有指定的域名或如何购买域，请参阅 [Internet domain management in Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx)（Azure AD 中的 Internet 域管理）。

### <a name="resolution-and-delegation"></a>解析和委托

有两种类型的 DNS 服务器：

* *权威* DNS 服务器托管 DNS 区域。 它只应答这些区域中的 DNS 记录查询。
* *递归* DNS 服务器不托管 DNS 区域。 它调用权威 DNS 服务器来收集所需的数据，以应答所有 DNS 查询。

Azure DNS 提供权威 DNS 服务。  它不提供递归 DNS 服务。 Azure 中的云服务和 VM 自动配置为使用 Azure 基础结构中单独提供的递归 DNS 服务。 有关如何更改这些 DNS 设置的详细信息，请参阅 [Name Resolution in Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)（Azure 中的名称解析）。

电脑或移动设备中的 DNS 客户端通常调用递归 DNS 服务器来执行客户端应用程序所需的任何 DNS 查询。

当递归 DNS 服务器收到 DNS 记录查询时（例如“www.contoso.com”），必须先找到托管“contoso.com”域的区域的名称服务器。 若要查找名称服务器，请从根名称服务器开始，接着查找托管“com”区域的名称服务器。 然后，查询“com”名称服务器，查找托管“contoso.com”区域的名称服务器。  最后，它便可以向这些名称服务器查询“www.contoso.com”。

此过程称为 DNS 名称解析。 严格地说，DNS 解析还有其他步骤，例如跟踪 CNAME，但这对于了解 DNS 委托的工作原理并不重要。

父区域如何“指向”子区域的名称服务器？ 方法是使用一种特殊的 DNS 记录，名为 NS 记录（NS 代表“名称服务器”）。 例如，根区域包含“com”的 NS 记录，并显示“com”区域的名称服务器。 而“com”区域又包含“contoso.com”的 NS 记录，其中显示“contoso.com”区域的名称服务器。 在父区域中设置子区域的 NS 记录称为委托域。

下图显示了一个 DNS 查询示例。 contoso.net 和 partners.contoso.net 为 Azure DNS 区域。

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. 客户端从其本地 DNS 服务器请求 `www.partners.contoso.net`。
1. 本地 DNS 服务器没有记录，因此向其根名称服务器发出请求。
1. 根名称服务器没有记录，但知道 `.net` 名称服务器的地址，于是将该地址提供给 DNS 服务器
1. DNS 将请求发送到 `.net` 名称服务器，该服务器没有记录，但是知道 contoso.net 名称服务器的地址。 本示例中为托管在 Azure DNS 中的 DNS 区域。
1. 区域 `contoso.net` 没有记录，但知道 `partners.contoso.net` 的名称服务器，于是用其进行响应。 本示例中为托管在 Azure DNS 中的 DNS 区域。
1. DNS 服务器从 `partners.contoso.net` 区域请求 `partners.contoso.net` 的 IP 地址。 它包含 A 记录，并使用 IP 地址进行响应。
1. DNS 服务器为客户端提供 IP 地址
1. 客户端连接到网站 `www.partners.contoso.net`。

每个委托实际上有两份 NS 记录：一份在父区域中指向子区域，另一份在子区域本身。 “contoso.net”区域包含“contoso.net”的 NS 记录（“net”中的 NS 记录除外）。 这些记录称为权威 NS 记录，位于子区域的顶点处。

## <a name="next-steps"></a>后续步骤

了解如何[将域委托给 Azure DNS](dns-delegate-domain-azure-dns.md)


