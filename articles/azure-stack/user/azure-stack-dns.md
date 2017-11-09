---
title: "Azure 堆栈中的 DNS |Microsoft 文档"
description: "Azure Stack 中的 DNS"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: ac93b5eb4228cef373428b7b69932d5993d54fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="dns-in-azure-stack"></a>Azure Stack 中的 DNS

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈包括以下 DNS 功能：
* DNS 主机名解析的支持
* 创建和管理 DNS 区域和记录使用 API

## <a name="support-for-dns-hostname-resolution"></a>DNS 主机名解析的支持
你可以指定公共 IP 资源，这将创建的映射的 DNS 域名标签*domainnamelabel.location*。 Azure 堆栈中的公共 IP 地址的 cloudapp.azurestack.external 托管 DNS 服务器。  

例如，如果你创建具有的公共 IP 资源**contoso**作为在本地 Azure 堆栈位置中，完全限定的域名 (FQDN) 域名标签**contoso.local.cloudapp.azurestack.external**解析为资源的公共 IP 地址。 此 FQDN 可用于创建自定义域指向 Azure 堆栈中的公共 IP 地址的 CNAME 记录。

> [!IMPORTANT]
> 创建每个域名标签必须在其 Azure 堆栈位置中唯一。

如果你创建使用门户的公共 IP 地址，如下所示：

![创建公共 IP 地址](media/azure-stack-whats-new-dns/image01.png)

此配置会很有用，如果你想要将与负载平衡的资源关联的公共 IP 地址。 例如，你可能必须处理来自 web 应用程序请求的负载平衡器。 负载平衡器是在一个或多个虚拟机上的网站。 现在可以访问该负载平衡的网站，通过为 DNS 名称，而不是 IP 地址。

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>创建和管理 DNS 区域和记录使用 API
你可以创建和管理 DNS 区域和 Azure 堆栈中的记录。  

Azure 堆栈提供 DNS 服务，如 Azure，使用 Api 与 Azure 的 DNS Api 相一致。  通过托管在 Azure 堆栈 DNS 域，你可以作为其他 Azure 服务管理你使用相同的凭据、 Api、 工具、 帐单和支持的 DNS 记录。 

原因很明显，Azure 堆栈 DNS 基础结构是比 Azure 的更为精简。 因此，作用域、 缩放和性能取决于 Azure 堆栈部署和部署的环境的规模。  因此，如性能、 可用性、 全局分发和高可用性 (HA) 而异部署部署。

## <a name="comparison-with-azure-dns"></a>Azure dns 的比较
Azure 堆栈中的 DNS 是类似于在 Azure 中，DNS 具有两种主要的例外情况：
* **它不支持 AAAA 记录**

    Azure 堆栈不支持 AAAA 记录，因为 Azure 堆栈不支持 IPv6 地址。  这是在 Azure 中的 DNS 和 Azure 堆栈的主要区别。
* **它不是多租户**

    与 Azure，Azure 堆栈中的 DNS 服务不是多租户。 所以每个租户不能创建相同的 DNS 区域。 仅尝试创建该区域的第一个订阅成功，并且后续请求失败。  它是一个已知的问题和 Azure 和 Azure 堆栈 DNS 有重要区别。 将从未来版本中解决此问题。

此外，有一些细微的差别，在 Azure 堆栈 DNS 标记、 元数据、 Etag 和限制的实现。

以下信息只适用于 Azure 堆栈 DNS，和从 Azure DNS 会稍有不同。 若要了解有关 Azure DNS 的详细信息，请参阅[DNS 区域和记录](../../dns/dns-zones-records.md)在 Microsoft Azure 文档站点。

### <a name="tags-metadata-and-etags"></a>标记、 元数据和 Etag

**标记**

Azure 堆栈 DNS 支持在 DNS 区域资源上使用 Azure 资源管理器标记。 它不支持标记上 DNS 记录集，尽管 DNS 记录因为在支持的替代方法元数据设置下一步所述。

**元数据**

记录集标记的替代方法，为 Azure 堆栈 DNS 支持对使用元数据的记录集进行批注。 与标记相类似，通过元数据可将名称/值对与每个记录集相关联。 例如，这可用于记录每个记录集的用途。 与标记不同的是，元数据不能用于提供 Azure 帐单的筛选视图，且不能在 Azure 资源管理器策略中指定。

**Etag**

假设两个人或两个进程尝试同时修改一条 DNS 记录。 哪一个占先？ 占先方是否知道他们/它们覆盖了其他人/进程创建的更改？

Azure 堆栈 DNS 使用 Etag 来安全地处理对同一资源的并发更改。 Etag 是单独从 Azure 资源管理器标记。 每个 DNS 资源（区域或记录集）都有与其相关联的 Etag。 只要检索资源，就会检索其 Etag。 在更新资源，你可以选择传回 Etag 以便 Azure 堆栈 DNS 可以验证 Etag 服务器匹配。 由于对资源的每次更新都会导致重新生成 Etag，Etag 不匹配表示发生了并发更改。 当创建新的资源时也可以使用 Etag，以确保该资源不存在。

默认情况下，Azure 堆栈 DNS PowerShell 使用 Etag 来阻止并发更改区域和记录集。 可选 -Overwrite 开关可用于取消 Etag 检查，这种情况下会覆盖发生的所有并发更改。

Azure DNS REST API，堆栈级别，使用 HTTP 标头指定的 Etag。 下表给出了它们的行为：

| 标头 | 行为|
|--------|---------|
| 无   | PUT 始终成功（没有 Etag 检查）|
| 如果匹配| 只有当资源存在并且 Etag 匹配时，PUT 才会成功|
| If-match *| 只有当资源存在时，PUT 才会成功|
| If-none-match *| 只有当资源不存在时，PUT 才会成功|

### <a name="limits"></a>限制

使用 Azure 堆栈 DNS 时，以下默认限制适用：

| 资源| 默认限制|
|---------|--------------|
| 每个订阅的区域数| 100|
| 每个区域的记录集数| 5000|
| 每个记录集的记录数| 20|

## <a name="next-steps"></a>后续步骤
[为 Azure 堆栈引入 Idn](azure-stack-understanding-dns.md)
