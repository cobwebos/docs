---
title: "Azure DNS 疑难解答指南 | Microsoft Docs"
description: "如何排查 Azure DNS 的常见问题"
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: jonatul
ms.translationtype: Human Translation
ms.sourcegitcommit: bae6cf7f5025936deba301dc4fd05f6fd5fd8fa6
ms.openlocfilehash: 1d9bb681a864bdc3e5a2f9c9a531d9566b16ada4
ms.contentlocale: zh-cn
ms.lasthandoff: 01/20/2017

---

# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS 疑难解答指南

此页介绍有关 Azure DNS 常见问题的疑难解答信息。

如果这些步骤未解决遇到的问题，还可以搜索 [MSDN 上的社区支持论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)或将你的问题发布在该论坛上。 或打开 Azure 支持请求。


## <a name="i-cant-create-a-dns-zone"></a>无法创建 DNS 区域

若要解决常见问题，请尝试下面的一个或多个步骤：

1.  查看 Azure DNS 审核日志以确定失败原因。
2.  每个 DNS 区域名称在其资源组中必须唯一。 即，一个资源组中不能具有名称相同的两个 DNS 区域。 请尝试使用不同的区域名称，或采用不同的资源组。
3.  可能会显示一个错误“已达到或超过订阅 {订阅 id} 中的区域的最大数量。” 请使用其他 Azure 订阅，删除一些区域，或者联系 Azure 支持部门以提高订阅限制。
4.  可能会显示一个错误“区域‘{区域名称}’不可用。” 此错误意味着 Azure DNS 无法为此 DNS 区域分配名称服务器。 请尝试使用不同的区域名称。 或者，如果是该域名的所有者，请联系 Azure 支持部门为你分配名称服务器。


### <a name="recommended-documents"></a>**建议的文档**

[DNS zones and records](dns-zones-records.md)
（DNS 区域和记录）<br>
[创建 DNS 区域](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>无法创建 DNS 记录

若要解决常见问题，请尝试下面的一个或多个步骤：

1.  查看 Azure DNS 审核日志以确定失败原因。
2.  该记录集是否已存在？  Azure DNS 使用记录集管理记录，记录集是具有相同名称和类型的记录的集合。 如果已存在名称和类型相同的记录，那么在添加另一此类记录时，应编辑现有记录集。
3.  希望在 DNS 区域顶点处（该区域的“根”）尝试创建记录？ 如果是这样，DNS 约定会使用 ‘@’ 字符作为记录名称。 另请注意，DNS 标准不允许在区域顶点创建 CNAME 记录。
4.  是否存在 CNAME 冲突？  DNS 标准不允许创建与其他类型记录的名称相同的 CNAME 记录。 如果已存在 CNAME 记录，则无法创建具有相同名称的其他类型的记录。  同样，如果创建的 CNAME 记录与现有其他类型记录的名称相匹配，则无法创建 CNAME 记录。 可通过删除另一条记录或选用不同的记录名称来解决此冲突。
5.  是否已达到 DNS 区域中允许的记录集数量上限？ 在 Azure 门户中此区域的“属性”下，显示有当前记录集数和最大记录集数。 如果已达此限制，则可删除一些记录集或联系 Azure 支持来提高此区域的记录集上限，然后重试。 


### <a name="recommended-documents"></a>**建议的文档**

[DNS zones and records](dns-zones-records.md)
（DNS 区域和记录）<br>
[创建 DNS 区域](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>无法解析 DNS 记录

DNS 名称解析是一个多步骤过程，该过程失败存在多种原因。 以下步骤有助于调查为何无法在 Azure DNS 托管的区域对 DNS 记录进行 DNS 解析。

1.  确保已在 Azure DNS 中正确配置 DNS 记录。 在 Azure 门户中查看 DNS 记录，检查区域名称、记录名称和记录类型是否正确。
2.  确保可在 Azure DNS 名称服务器上正确解析 DNS 记录。
    - 如果从本地电脑查询 DNS，可能会发现缓存结果未反映名称服务器当前的状态。  此外，企业网络通常使用 DNS 代理服务器，这些服务器会阻止 DNS 查询定向到特定名称服务器。  若要避免这些问题，请使用基于 Web 的名称解析服务，例如 [digwebinterface](http://digwebinterface.com)。
    - 请务必为 DNS 区域指定正确的名称服务器，如 Azure 门户中所示。
    - 检查 DNS 名称是否正确（必须指定完全限定的名称，包括区域名称），以及记录类型是否正确
3.  确保 DNS 域名已正确[委托给 Azure DNS 名称服务器](dns-domain-delegation.md)。 存在[许多提供 DNS 委托验证的第三方网站](https://www.bing.com/search?q=dns+check+tool)。 这是区域委派测试，因此应只输入 DNS 区域名称，而不是完全限定的记录名称。
4.  完成上述步骤后，现在应可以正确解析 DNS 记录。 若要进行验证，可再次使用 [digwebinterface](http://digwebinterface.com)，这次请使用默认名称服务器设置。


### <a name="recommended-documents"></a>**建议的文档**

[将域委托给 Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>如何为 SRV 记录指定“服务”和“协议”？

Azure DNS 以记录集方式管理记录，记录集是具有相同名称和类型的记录的集合。 对于 SRV 记录集，需将“服务”和“协议”指定为记录集名称的一部分。 对于记录集中的每条记录，需单独指定其他 SRV 参数（“priority”、“weight”、“port”和“target”）。

示例 SRV 记录名称 (service name 'sip', protocol 'tcp')：

- \_sip.\_tcp（在区域顶点创建一个记录集）
- \_sip.\_tcp.sipservice（创建名为“sipservice”的记录集）

### <a name="recommended-documents"></a>**建议的文档**

[DNS zones and records](dns-zones-records.md)
（DNS 区域和记录）<br>
[Create DNS record sets and records by using the Azure portal](dns-getstarted-create-recordset-portal.md)
（使用 Azure 门户创建 DNS 记录集和记录）<br>
[SRV 记录类型 (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>后续步骤

* 了解 [ Azure DNS 区域和记录](dns-zones-records.md)
* 若要开始使用 Azure DNS，请了解如何[创建 DNS 区域](dns-getstarted-create-dnszone-portal.md)和[创建 DNS 记录](dns-getstarted-create-recordset-portal.md)。
* 若要迁移现有 DNS 区域，请了解如何[导入和导出 DNS 区域文件](dns-import-export.md)。


