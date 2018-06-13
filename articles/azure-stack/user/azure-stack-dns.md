---
title: Azure Stack 中的 DNS | Microsoft Docs
description: 使用 Azure 堆栈中的 DNS
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196519"
---
# <a name="using-dns-in-azure-stack"></a>使用 Azure 堆栈中的 DNS

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈支持以下域名系统 (DNS) 功能：

* DNS 主机名解析
* 创建和管理 DNS 区域和记录使用 API

## <a name="support-for-dns-hostname-resolution"></a>支持 DNS 主机名解析

你可以指定公共 IP 资源的 DNS 域名称标签。 Azure 堆栈使用*domainnamelabel.location*。 标签名称和到公用 IP 地址在 Azure 堆栈中的映射 cloudapp.azurestack.external 托管 DNS 服务器。

例如，如果你创建具有的公共 IP 资源**contoso**作为在本地 Azure 堆栈位置中，域名标签[完全限定的域名称](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN) **contoso.local.cloudapp.azurestack.external**解析为资源的公共 IP 地址。 可以使用此 FQDN 创建自定义域 CNAME 记录，该记录指向 Azure Stack 中的公共 IP 地址。

若要了解有关名称解析的详细信息，请参阅[DNS 解析](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)文章。

> [!IMPORTANT]
> 你创建每个域名标签必须在其 Azure 堆栈位置中唯一。

下一个屏幕捕获显示**创建公共 IP 地址**对话框用于创建使用门户的公共 IP 地址。

![创建公共 IP 地址](media/azure-stack-whats-new-dns/image01.png)

**示例方案**

必须处理来自 web 应用程序请求的负载平衡器。 负载平衡器是一个或多个虚拟机上运行的网站。 你可以访问负载平衡使用网站的 DNS 名称，而不是 IP 地址。

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>创建和管理 DNS 区域和记录使用 API

可以在 Azure Stack 中创建和管理 DNS 区域和记录。

Azure Stack 使用与 Azure DNS API 一致的 API 提供与 Azure 类似的 DNS 服务。  通过托管在 Azure 堆栈 DNS 域，你可以管理你使用相同的凭据、 Api 和工具的 DNS 记录。 你可以使用相同的计费和支持作为其他 Azure 服务。

Azure 堆栈 DNS 基础结构是比 Azure 的更为精简。 大小和 Azure 堆栈部署的位置将影响 DNS 作用域、 缩放和性能。 这也意味着性能、 可用性、 全局分发和高可用性可以改变部署部署。

## <a name="comparison-with-azure-dns"></a>与 Azure DNS 比较

Azure 堆栈中的 DNS 是类似于在 Azure 中，DNS 但有主要您需要了解的异常。

* **不支持 AAAA 记录**

    Azure Stack 不支持 AAAA 记录，因为 Azure Stack 不支持 IPv6 地址。  这是 Azure DNS 与 Azure Stack DNS 之间的主要差异。
* **不是多租户**

    Azure 堆栈中的 DNS 服务不是多租户。 每个租户不能创建相同的 DNS 区域。 仅首个订阅尝试创建区域会成功，后续请求都会失败。  这是已知问题，也是 Azure DNS 和 Azure Stack DNS 之间的主要差异。 此问题将在未来版本中解决。
* **标记、 元数据和 Etag**

    有细微的差异，标记、 元数据、 Etag 时，和限制 Azure 堆栈的处理方式。

若要了解有关 Azure DNS 的详细信息，请参阅[DNS 区域和记录](../../dns/dns-zones-records.md)。

### <a name="tags-metadata-and-etags"></a>标记、元数据和 Etag

**标记**

Azure Stack DNS 支持在 DNS 区域资源上使用 Azure 资源管理器标记。 它不支持在 DNS 记录集上使用标记，不过作为替代方法，在 DNS 记录集上支持“元数据”，如下所述。

**元数据**

作为记录集标记的替代方法，Azure Stack DNS 支持使用“元数据”批注记录集。 与标记相类似，通过元数据可将名称/值对与每个记录集相关联。 这非常有用，例如可用于记录每个记录集的用途。 与标记不同的是，元数据不能用于提供 Azure 帐单的筛选视图，且不能在 Azure 资源管理器策略中指定。

**Etag**

假设两个人或两个进程尝试同时修改一条 DNS 记录。 哪一个占先？ 占先方是否知道他们/它们覆盖了其他人/进程创建的更改？

Azure Stack DNS 使用 Etag 来安全地处理对同一资源的并发更改。 Etag 与 Azure 资源管理器“标记”不同。 每个 DNS 资源（区域或记录集）都有与其相关联的 Etag。 只要检索资源，就会检索其 Etag。 更新资源时，可以选择传递回 Etag 以便 Azure Stack DNS 可以验证服务器上的 Etag 是否匹配。 由于对资源的每次更新都会导致重新生成 Etag，Etag 不匹配表示发生了并发更改。 当创建新的资源时也可以使用 Etag，以确保该资源不存在。

默认情况下，Azure Stack DNS PowerShell 使用 Etag 来阻止对区域和记录集的并发更改。 可选 -Overwrite 开关可用于取消 Etag 检查，这种情况下会覆盖发生的所有并发更改。

Etag 是在 Azure Stack DNS REST API 级别使用 HTTP 标头指定的。 下表给出了它们的行为：

| 标头 | 行为|
|--------|---------|
| 无   | PUT 始终成功（没有 Etag 检查）|
| If-match| 只有当资源存在并且 Etag 匹配时，PUT 才会成功|
| If-match *| 只有当资源存在时，PUT 才会成功|
| If-none-match *| 只有当资源不存在时，PUT 才会成功|

### <a name="limits"></a>限制

使用 Azure Stack DNS 时，以下默认限制适用：

| 资源| 默认限制|
|---------|--------------|
| 每个订阅的区域数| 100|
| 每个区域的记录集数| 5000|
| 每个记录集的记录数| 20|

## <a name="next-steps"></a>后续步骤

[适用于 Azure Stack 的 iDNS 简介](azure-stack-understanding-dns.md)
