---
title: 将 Azure DNS 用于专用域 | Microsoft Docs
description: Microsoft Azure 上的专用 DNS 托管服务概述。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 1c805819a22d26e650d13b0e41ebac00c4e52d91
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="using-azure-dns-for-private-domains"></a>将 Azure DNS 用于专用域
域名系统（或称为 DNS）负责将服务名称转换（或解析）为其 IP 地址。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。  除了面向 Internet 的 DNS 域之外，作为一项预览版功能，Azure DNS 现在还支持专用 DNS 域。  
 
Azure DNS 提供了可靠、安全的 DNS 服务来管理和解析虚拟网络中的域名，无需添加自定义 DNS 解决方案。 专用 DNS 区域允许使用自定义域名而不使用当前可用的由 Azure 提供的名称。  使用自定义域名可帮助你定制虚拟网络体系结构以便最好地满足组织需求。 它在虚拟网络内以及在虚拟网络之间针对 VM 提供名称解析。 除此之外，还可以通过水平分割视图配置区域名称，从而允许专用和公用 DNS 区域共享相同的名称。

![DNS 概述](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>优点

* **无需使用自定义 DNS 解决方案。** 以前，许多客户创建了自定义 DNS 解决方案来管理其虚拟网络。  现在可以使用 Azure 的本机基础结构执行 DNS 区域管理，这解除了创建和管理自定义 DNS 解决方案的负担。

* **使用所有常见的 DNS 记录类型。**  Azure DNS 支持 A、AAAA、CNAME、MX、NS、PTR、SOA、SRV 和 TXT 记录。

* **自动化主机名记录管理。** 除了承载自定义 DNS 记录之外，Azure 还会自动维护指定虚拟网络中的 VM 的主机名记录。  这使得你可以优化所使用的域名，不需要创建自定义 DNS 解决方案或修改应用程序。

* **虚拟网络之间的主机名解析。** 不同于 Azure 提供的主机名，专用 DNS 区域可以在虚拟网络之间共享。  此功能简化了跨网络和服务发现方案，例如，虚拟网络对等互连。

* **熟悉的工具和用户体验。** 为了降低学习难度，此新的产品/服务使用已经成熟的 Azure DNS 工具（PowerShell、资源管理器模板、REST API）。

* **水平分割 DNS 支持。** Azure DNS 允许使用相同的名称创建在虚拟网络内与在公共 Internet 内分别解析为不同结果的区域。  水平分割 DNS 的典型方案是提供一个专用服务版本以在虚拟网络内部使用。

* **在所有 Azure 区域中可用。** Azure 公有云中的所有 Azure 区域均已推出 Azure DNS 专用区域。 


## <a name="capabilities"></a>功能 
* 将链接到专用区域的单个虚拟网络中的虚拟机自动注册为一个“注册”虚拟网络。 虚拟机将作为指向其专用 IP 的 A 记录注册（添加）到专用区域。 而且，当删除“注册”虚拟网络中的虚拟机时，Azure 还将从所链接的专用区域中删除对应的 DNS 记录。 注意，“注册”虚拟网络还默认充当“解析”虚拟网络，因为针对该区域的 DNS 解析将从该“注册”虚拟网络中的任何虚拟机进行。 
* 在作为“解析”虚拟网络链接到专用区域的虚拟网络之间支持正向 DNS 解析。 对于跨虚拟网络 DNS 解析，不会明确要求虚拟网络彼此对等互连。 不过，对于其他场景（例如 HTTP 流量），客户可能希望将虚拟网络对等互连。
* 在 VNET 范围内支持反向 DNS 查找。 对分配到专用区域的虚拟网络中的专用 IP 进行反向 DNS 查找将返回 FQDN，其中包括主机/记录名称以及作为后缀的区域名称。 


## <a name="limitations"></a>限制
* 每个专用区域 1 个“注册”虚拟网络
* 每个专用区域最多 10 个“解析”虚拟网络
* 一个给定的虚拟网络只能作为注册虚拟网络链接到一个专用区域
* 一个给定的虚拟网络可以作为解析虚拟网络链接到最多 10 个专用区域
* 如果指定了注册虚拟网络，则无法通过 Powershell/CLI/API 查看或检索注册到专用区域的虚拟网络中的 VM 的 DNS 记录，但是，VM 记录确实已注册并且会成功解析
* 反向 DNS 仅适用于“注册”虚拟网络中的专用 IP 空间
* 对未在专用区域中注册的专用 IP 地址（例如：作为“解析”虚拟网络链接到专用区域的虚拟网络中的虚拟机的专用 IP）进行反向 DNS 查找将返回“internal.cloudapp.net”作为 DNS 后缀，不过此后缀将不可解析。   
* 在最初（即首次） 作为“注册”或“解析”虚拟网络链接到专用区域时， 虚拟网络需要是空的（即没有 VM 记录）。 但是，以后作为注册或解析虚拟网络链接到其他专用区域时，虚拟网络可以是非空的。 
* 目前不支持条件转发，例如，为了启用 Azure 与本地网络之间的解析。 有关客户可以如何通过其他机制实现此方案的文档，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

建议你同时研究一下[常见问题解答](./dns-faq.md#private-dns)来了解有关 Azure DNS 中的专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定 DNS 注册和解析行为。 


## <a name="pricing"></a>定价

在公共预览版期间，专用 DNS 区域免费。 在公开上市期间，此功能将使用基于使用量的定价模型，这与现有的 Azure DNS 产品/服务类似。 


## <a name="next-steps"></a>后续步骤

了解如何使用 [PowerShell](./private-dns-getstarted-powershell.md) 或 [CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

研究一下可以通过 Azure DNS 中的专用区域实现的一些常见方案：[专用区域方案](./private-dns-scenarios.md)。

研究一下[常见问题解答](./dns-faq.md#private-dns)来了解有关 Azure DNS 中的专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为。 

若要了解 DNS 区域和记录，请访问 [DNS 区域和记录概述](dns-zones-records.md)。

了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。

