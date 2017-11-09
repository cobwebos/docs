---
title: "查看 Azure 堆栈中的公共 IP 地址使用 |Microsoft 文档"
description: "管理员可以在一个区域中查看的公共 IP 地址的消耗"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>查看 Azure 堆栈中的公共 IP 地址使用

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为云管理员，你可以查看已分配给租户，分配时，仍然可用的公共 IP 地址数和已在该位置中已分配的公共 IP 地址的百分比的公共 IP 地址数。

**公共 IP 池使用情况**磁贴显示已耗用跨所有的公共 IP 地址池在结构上的公共 IP 地址的总数，是否使用为租户 IaaS VM 实例，fabric 基础结构服务或显式创建的租户的公共 IP 地址资源。

此磁贴的目的是总数的为 Azure 堆栈管理员提供的意义上的已用完在此位置中的公共 IP 地址。 这可帮助确定是否它们运行的低在此资源上的管理员。

上**资源提供程序**，**网络**边栏选项卡，**公共 IP 地址**下的菜单项**租户资源**列出仅这些公共IP 地址已*明确创建的租户*。 因此，数**使用**公共 IP 地址上**公共 IP 池使用情况**磁贴始终是不同于 （大于） 上的数字**公共 IP 地址**磁贴下**租户资源**。

## <a name="view-the-public-ip-address-usage-information"></a>查看公共 IP 地址使用情况信息
若要查看已耗用的区域中的公共 IP 地址的总数：

1. 在 Azure 堆栈管理员门户中，单击**更多的服务**下**管理资源**，单击**资源提供程序**。
2. 从列表中**资源提供程序**，选择**网络**。
3. **网络**边栏选项卡显示**公共 IP 池使用情况**磁贴中**概述**部分。

![网络资源提供程序边栏选项卡](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

请记住，**使用**数字表示从所有公共 IP 地址池在该位置分配公共 IP 地址数。 **免费**数字表示的数的公共 IP 地址从所有公共 IP 地址池未分配并仍然可用。 **%Used**的数字的数字表示，使用或地址分配所有的公共 IP 地址池在该位置中的公共 IP 地址的总数的百分比。

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>查看已通过租户订阅中创建的公共 IP 地址
若要查看的显式创建的特定区域中的租户订阅的公共 IP 地址列表，请单击**公共 IP 地址**下**租户资源**。

![租户公共 IP 地址](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

你可能注意到某些已动态分配的公共 IP 地址显示在列表中，但没有尚未与之关联的地址。 这是因为地址资源中网络资源提供程序，但不是在网络控制器尚未创建。

网络控制器不会对此资源分配一个地址，直到它实际绑定到一个接口、 网络接口卡 (NIC)、 负载平衡器或虚拟网络网关。 当公共 IP 地址绑定到一个接口，网络控制器分配到其中，一个 IP 地址并出现在**地址**字段。

## <a name="view-the-public-ip-address-information-summary-table"></a>视图的公共 IP 地址信息摘要表
有在其中分配公共 IP 地址的不同确定是否地址将出现在一个列表或另一个事例的数量。

| **公共 IP 地址分配用例** | **将出现在使用情况摘要** | **将出现在租户公共 IP 地址列表** |
| --- | --- | --- |
| 尚未分配给一个 NIC 或负载平衡器 （临时） 的动态公共 IP 地址 |否 |是 |
| 分配给一个 NIC 或负载平衡器的动态的公共 IP 地址。 |是 |是 |
| 分配给租户 NIC 或负载平衡器的静态的公共 IP 地址。 |是 |是 |
| 分配给结构基础结构服务终结点的静态的公共 IP 地址。 |是 |否 |
| 公共 IP 地址隐式为 IaaS VM 实例创建，并且用于出站 NAT 虚拟网络上。 这些是租户创建 VM 实例，以便 Vm 可以将信息发送到 Internet 时，在后台创建。 |是 |否 |

## <a name="next-steps"></a>后续步骤
[管理 Azure 堆栈中的存储帐户](azure-stack-manage-storage-accounts.md)