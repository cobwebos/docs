---
title: 了解 Azure 堆栈中的 Idn |Microsoft 文档
description: 了解 Idn 特性和 Azure 堆栈中的功能
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: df9c22877eeac381d936f2fb86f5720c9cc9c930
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="introducing-idns-for-azure-stack"></a>适用于 Azure Stack 的 iDNS 简介

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Idn 是一项 Azure 堆栈网络功能，可用于解析外部 DNS 名称 (例如，http://www.bing.com.)它还允许你注册内部虚拟网络名称。 通过这样做，你可以通过名称而不是 IP 地址，Vm 在同一虚拟网络上解决。 此方法不再需要提供自定义 DNS 服务器条目。 有关 DNS 的详细信息，请参阅[Azure DNS 概述](https://docs.microsoft.com/en-us/azure/dns/dns-overview)。

## <a name="what-does-idns-do"></a>iDNS 有什么作用？

使用 Azure Stack 中的 iDNS 可以获得以下功能，而无需指定自定义 DNS 服务器条目：

* 适用于租户工作负荷的共享 DNS 名称解析服务。
* 适用于租户虚拟网络内的名称解析和 DNS 注册的权威 DNS 服务。
* 从租户 VM 解析 Internet 名称的递归 DNS 服务。 租户不再需要指定自定义 DNS 条目以解决 Internet 名称 (例如，www.bing.com。)

你仍可以将你自己的 DNS，并使用自定义 DNS 服务器。 但是，通过使用 Idn，你可将 Internet DNS 名称解析和连接到同一虚拟网络中的其他 Vm，你不需要创建自定义 DNS 条目。

## <a name="what-doesnt-idns-do"></a>Idn 并不执行什么操作？

哪些 Idn 不允许你这样做，是创建名称，它们可以是从虚拟网络外部解析的 DNS 记录。

在 Azure 中，必须指定与公共 IP 地址相关联的 DNS 名称标签的选项。 你可以选择标签（前缀），但 Azure 会根据创建公共 IP 地址所在的区域选择后缀。

![DNS 名称标签示例](media/azure-stack-understanding-dns-in-tp2/image3.png)

如上图所示，Azure 将创建一个"A"记录在 DNS 中为在该区域下指定的 DNS 名称标签**westus.cloudapp.azure.com**。前缀和后缀结合来撰写[完全限定的域名称](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN)，可以从任意位置在上解析公共 Internet。

Azure Stack 仅支持用于内部名称注册的 iDNS，因此它无法执行以下操作：

* 创建 DNS 记录下现有托管 DNS 区域 (例如，local.azurestack.external。)
* 创建 DNS 区域 （例如 Contoso.com。)
* 在你自己的自定义 DNS 区域下创建记录。
* 支持购买域名。

## <a name="next-steps"></a>后续步骤

[使用 Azure 堆栈中的 DNS](azure-stack-dns.md)