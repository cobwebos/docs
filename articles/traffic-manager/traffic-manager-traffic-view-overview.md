---
title: "Azure 流量管理器中的流量视图 | Microsoft Docs"
description: "流量管理器流量视图简介"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1ab98ab173edc794bfabdf55d950ac689a37c0a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="traffic-manager-traffic-view"></a>流量管理器流量视图

>[!NOTE]
>流量管理器中的“流量视图”功能以公共预览版形式提供，并且其可用性和可靠性与正式发布版本中的功能可能不在同一级别。 此功能不受支持，可能存在功能限制，并且可能不是在所有 Azure 区域都可用。 有关此功能可用性和状态方面的最新通知，请参阅 [Azure 流量管理器更新](https://azure.microsoft.com/updates/?product=traffic-manager)页。

流量管理器提供 DNS 级别路由，以便根据创建配置文件时设置的路由方法将最终用户定向到正常运行的终结点。 这将为流量管理器提供用户群视图（在 DNS 解析程序粒度级别）及其流量模式。 启用流量视图时，系统将处理此信息以提供可执行的建议。 

使用流量视图，可以：
- 了解用户群所在的位置（最多可达本地 DNS 解析程序级别粒度）。
- 了解来自这些区域的流量。
- 了解什么是这些用户所体验的代表性延迟。
- 深入研究特定流量模式，从其中每个用户群到你拥有终结点的 Azure 区域。 

例如，可以使用流量视图来了解哪些区域具有大量流量，但遭受较长的延迟。 接下来，可以使用此信息来计划将占用空间扩展到新的 Azure 区域，使这些用户可以体验到较短的延迟。

## <a name="how-traffic-view-works"></a>流量视图的工作原理

流量管理器的工作原理是根据已启用此功能的配置文件，查看在过去七天内收到的传入查询。 它从该信息提取 DNS 解析程序的源 IP，然后使用该 IP 作为用户位置的表示形式。 然后以 DNS 解析程序级别粒度将这些 IP 组合在一起，以使用流量管理器维护的 IP 地址的地理信息创建用户群区域。 然后，流量管理器查找查询所路由到的 Azure 区域，并为这些区域中的用户构造流量流映射。
在下一步中，流量管理器使用它为不同最终用户网络维护的网络智能延迟表将用户群区域关联到 Azure 区域映射，以了解这些区域中的用户在连接到 Azure 区域时所经历的平均延迟。 然后按每个本地 DNS 解析程序 IP 级别将所有这些计算表格化，再向你显示。 你可以在所选的分析工作流中处理这些信息，也可以将这些信息下载为 CSV 文件。
使用流量视图时，将根据用于创建所显示建议的数据点数目进行计费。 目前，所用的唯一数据点类型是根据流量管理器配置文件接收的查询。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。


## <a name="next-steps"></a>后续步骤

- 了解[流量管理器工作原理](traffic-manager-overview.md)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)


