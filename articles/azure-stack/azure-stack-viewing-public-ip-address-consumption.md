---
title: 查看 Azure 堆栈中的公共 IP 地址使用 |Microsoft 文档
description: 管理员可以在一个区域中查看的公共 IP 地址的消耗
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742452"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>查看 Azure 堆栈中的公共 IP 地址使用

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为云管理员，你可以查看：
 - 已分配给租户的公共 IP 地址数。
 - 仍可用于分配的公共 IP 地址数。
 - 已在该位置中已分配的公共 IP 地址的百分比。

**公共 IP 池使用情况**磁贴将显示在公共 IP 地址池之间使用的公共 IP 地址数。 每个 IP 地址，该磁贴显示租户 IaaS VM 的使用情况实例、 fabric 基础结构服务和显式创建的租户的公共 IP 地址资源。

磁贴的目的是使在此位置中使用的公共 IP 地址数的意义上的 Azure 堆栈运算符。 数可帮助确定是否它们运行的低在此资源上的管理员。

**公共 IP 地址**下的菜单项**租户资源**列出仅这些公共 IP 地址已*明确创建的租户*。 您可以在找到菜单项**资源提供程序**，**网络**窗格。 数**使用**公共 IP 地址上**公共 IP 池使用情况**磁贴始终是不同于 （大于） 上的数字**公共 IP 地址**下磁贴**租户资源**。

## <a name="view-the-public-ip-address-usage-information"></a>查看公共 IP 地址使用情况信息
若要查看已耗用的区域中的公共 IP 地址的总数：

1. 在 Azure 堆栈管理员门户中，选择**更多的服务**下**管理资源**，选择**资源提供程序**。
2. 从列表中**资源提供程序**，选择**网络**。
3. **网络**窗格显示**公共 IP 池使用情况**磁贴中**概述**部分。

![网络资源提供程序窗格](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**使用**数字表示的号从公共 IP 地址池分配公共 IP 地址。 **免费**数字表示的数的公共 IP 地址从公共 IP 地址池未分配并仍然可用。 **%Used**的数字的数字表示，使用或地址分配公共 IP 地址池，在该位置中的公共 IP 地址的总数的百分比。

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>查看已通过租户订阅中创建的公共 IP 地址
选择**公共 IP 地址**下**租户资源**。 查看的显式创建的特定区域中的租户订阅的公共 IP 地址的列表。

![租户公共 IP 地址](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

你可能注意到某些已动态分配的公共 IP 地址显示在列表中。 但是，一个地址尚未与它们关联尚未。 在网络资源提供程序，但尚未在网络控制器中已创建地址资源。

网络控制器不会对资源分配一个地址，直到它绑定到一个接口、 网络接口卡 (NIC)、 负载平衡器或虚拟网络网关。 公共 IP 地址将绑定到一个接口，网络控制器分配的 IP 地址。 地址将出现在**地址**字段。

## <a name="view-the-public-ip-address-information-summary-table"></a>视图的公共 IP 地址信息摘要表
在不同情况下，公共 IP 地址分配，以确定是否地址将出现在一个列表或另一个。

| **公共 IP 地址分配用例** | **将出现在使用情况摘要** | **将出现在租户公共 IP 地址列表** |
| --- | --- | --- |
| 尚未分配给一个 NIC 或负载平衡器 （临时） 的动态公共 IP 地址 |“否” |“是” |
| 分配给一个 NIC 或负载平衡器的动态的公共 IP 地址。 |“是” |“是” |
| 分配给租户 NIC 或负载平衡器的静态的公共 IP 地址。 |“是” |“是” |
| 分配给结构基础结构服务终结点的静态的公共 IP 地址。 |“是” |“否” |
| 公共 IP 地址隐式为 IaaS VM 实例创建，并且用于出站 NAT 虚拟网络上。 这些是租户创建 VM 实例，以便 Vm 可以将信息发送到 Internet 时，在后台创建。 |“是” |“否” |

## <a name="next-steps"></a>接下来的步骤
[管理 Azure 堆栈中的存储帐户](azure-stack-manage-storage-accounts.md)