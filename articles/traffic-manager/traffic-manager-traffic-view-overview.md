---
title: Azure 流量管理器中的流量视图 | Microsoft Docs
description: 流量管理器流量视图简介
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 7ce51017fdee92e5589c06b398c9650930d5436d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179831"
---
# <a name="traffic-manager-traffic-view"></a>流量管理器流量视图

流量管理器提供 DNS 级别路由，用于根据创建配置文件时指定的路由方法将最终用户定向到正常运行的终结点。 流量视图为流量管理器提供用户群视图（在 DNS 解析程序粒度级别）及其流量模式。 启用流量视图时，系统将处理此信息以提供可执行的建议。 

使用流量视图，可以：
- 了解用户群所在的位置（最多可达本地 DNS 解析程序级别粒度）。
- 查看源自这些区域的流量（以 Azure 流量管理器处理的 DNS 查询形式监测）。
- 深入了解什么是这些用户体验到的典型延迟。
- 深入研究特定流量模式，从其中每个用户群到你拥有终结点的 Azure 区域。 

例如，可以使用流量视图来了解哪些区域具有大量流量，但遭受较长的延迟。 接下来，可以使用此信息来计划将占用空间扩展到新的 Azure 区域，使这些用户可以体验到较短的延迟。

## <a name="how-traffic-view-works"></a>流量视图的工作原理

流量管理器的工作原理是根据已启用此功能的配置文件，查看在过去七天内收到的传入查询。 流量视图从传入查询信息提取 DNS 解析程序的源 IP，使用该 IP 作为用户位置的表示形式。 然后以 DNS 解析程序级别粒度将这些 IP 组合在一起，以使用流量管理器维护的 IP 地址的地理信息创建用户群区域。 然后，流量管理器查找查询所路由到的 Azure 区域，并为这些区域中的用户构造流量流映射。  
在下一步中，流量管理器使用它为不同最终用户网络维护的网络智能延迟表将用户群区域关联到 Azure 区域映射，以了解这些区域中的用户在连接到 Azure 区域时所经历的平均延迟。 然后按每个本地 DNS 解析程序 IP 级别将所有这些计算组合起来，再向你显示。 可以通过各种方式来使用信息。

>[!NOTE]
>流量视图中描述的延迟是最终用户和其所连接到的 Azure 区域之间的代表性延迟，不是 DNS 查找延迟。 流量视图对本地 DNS 解析程序与查询被路由到的 Azure 区域之间的延迟进行了最佳估算，如果可用数据不足，返回的延迟将为 NULL。 

## <a name="visual-overview"></a>视觉概览

导航到“流量管理器”页中的“流量视图”部分时，会看到一个地理图，其中包含流量视图见解。 该图提供了有关用户群以及流量管理器配置文件终结点的信息。

### <a name="user-base-information"></a>用户群信息

对于那些可以使用位置信息的本地 DNS 解析程序，用户群信息显示在图中。 DNS 解析程序的颜色表示那些使用 DNS 解析程序进行流量管理器查询的最终用户体验到的平均延迟。

如果将鼠标悬停在图中 DNS 解析程序位置上方，则会显示：
- DNS 解析程序的 IP 地址
- 流量管理器从其看到的 DNS 查询流量
- 流量从 DNS 解析程序路由到的终结点，是终结点和 DNS 解析程序之间的一条线 
- 从该位置到终结点的平均延迟，以连接二者的线的颜色来表示

### <a name="endpoint-information"></a>终结点信息

终结点所在的 Azure 区域在图中显示为蓝色点。 如果终结点在外部，且不存在映射到该终结点的 Azure 区域，则终结点显示在图顶部。 单击任意终结点即可查看不同的位置（基于所使用的 DNS 解析程序），流量从这些位置引导到该终结点。 连接显示为终结点和 DNS 解析程序位置之间的一条线，其颜色取决于该对之间的代表性延迟。 此外，还可以看到终结点的名称、其运行时所在的 Azure 区域、通过流量管理器配置文件引导到该区域的请求的总量。


## <a name="tabular-listing-and-raw-data-download"></a>表格式列表和原始数据下载

可以在 Azure 门户中以表格格式查看流量视图数据。 每个 DNS 解析程序 IP /终结点对都有一个条目，用于显示 DNS 解析程序的 IP 地址、终结点所在的 Azure 区域的名称和地理位置（如果提供）、发送到该终结点且与该 DNS 解析程序关联的请求量以及与使用该 DNS 的最终用户关联的代表性延迟（如果提供）。 也可将流量视图数据下载为 CSV 文件，该文件可以用作所选分析工作流的一部分。

## <a name="billing"></a>计费

使用流量视图时，将根据用于创建所显示建议的数据点数目进行计费。 目前，所用的唯一数据点类型是根据流量管理器配置文件接收的查询。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。


## <a name="next-steps"></a>后续步骤

- 了解[流量管理器工作原理](traffic-manager-overview.md)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)

