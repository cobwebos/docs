---
title: "了解 Azure 堆栈中的 DNS |Microsoft 文档"
description: "了解 DNS 功能和 Azure 堆栈中的功能"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 86ed2805e93bd147841e22a773b52d1451f8c353
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="introducing-idns-for-azure-stack"></a>为 Azure 堆栈引入 Idn

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Idn 是一项功能，可用于解析外部 DNS 名称 （如 http://www.bing.com) Azure 堆栈中。
它还允许你注册内部虚拟网络名称。 通过这样做，即可解决 Vm 在同一虚拟网络上的名称，而不是 IP 地址，而无需提供自定义 DNS 服务器条目。

它是指一直都有在 Azure 中，但是位于 Windows Server 2016 和 Azure 堆栈过。

## <a name="what-does-idns-do"></a>Idn 的作用是什么？
使用 Idn Azure 堆栈中，你可以获得以下功能，而无需指定自定义 DNS 服务器条目：

* 共享租户工作负载的 DNS 名称解析服务。
* 名称解析和 DNS 注册租户虚拟网络内的权威 DNS 服务。
* 从租户 Vm Internet 名称解析的递归 DNS 服务。 租户不再需要指定自定义 DNS 条目以解决 Internet 名称 (例如，www.bing.com)。

你仍可以将你自己的 DNS 和如果你想使用自定义 DNS 服务器。 但现在，如果您只想要能够解析 Internet DNS 名称，并且能够连接到同一虚拟网络中的其他虚拟机，无需指定任何其他选项，它还会正常工作。

## <a name="what-does-idns-not-do"></a>Idn 不做什么？
哪些 Idn 不允许你执行是创建名称，它们可以是从虚拟网络外部解析的 DNS 记录。

在 Azure 中，你可以指定可以与公共 IP 地址相关联的 DNS 名称标签的选择。 你可以选择的标签 （前缀），但 Azure 选择的后缀，基于在其中创建公共 IP 地址的区域。

![屏幕快照的 DNS 名称标签](media/azure-stack-understanding-dns-in-tp2/image3.png)

在上图中，Azure 将创建的"A"记录在 DNS 中在该区域下指定的 DNS 名称标签**westus.cloudapp.azure.com**。前缀和后缀一起构成完全限定域名名称 (FQDN)，可以从任意位置在上解析公共 Internet。

Azure 堆栈仅支持 Idn 的内部名称注册，因此无法执行以下：

* 创建现有托管 DNS 区域 (例如，local.azurestack.external) 下的 DNS 记录。
* 创建 DNS 区域 （例如 Contoso.com)。
* 创建你自己的自定义 DNS 区域下一个记录。
* 支持域名的购买。

