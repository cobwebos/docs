---
title: "关于 Log Analytics 中的网络监视 | Microsoft Docs"
description: "概述了网络监视解决方案，包括 NPM，它们用来在云、本地和混合环境中管理网络。"
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>网络监视解决方案 

Azure 提供了许多解决方案来监视网络资产。 Azure 具有相应的解决方案和实用程序来监视网络连接、ExpressRoute 线路的运行状况，以及分析云中的网络流量。

## <a name="network-performance-monitor-npm"></a>网络性能监视器 (NPM)

网络性能监视器 (NPM) 是一个功能套件，其中的每个功能分别用于监视网络的运行状况、应用程序的网络连接，以及深入了解网络性能。 NPM 基于云并提供了一个混合的网络监视解决方案，用于监视以下各项之间的连接：
 
* 云部署和本地位置
* 多个数据中心和分支机构
* 任务关键型多层应用程序/微服务
* 用户位置和基于 web 的应用程序 (HTTP/HTTPs) 

## <a name="performance-monitor"></a>性能监视器

性能监视器是 NPM 的一部分，是适用于云、混合和本地环境的网络监视功能。 你可以跨远程分支和现场办事处、门店位置、数据中心和云来监视网络连接。 可以在你的用户产生抱怨之前检测到网络问题。 主要优势包括：

* 跨各种子网监视数据丢失和延迟情况并设置警报
* 监视网络上的所有路径（包括冗余路径）
* 对难以复制且在特定时间点出现的暂时性网络问题进行故障排除
* 确定网络上导致性能下降的特定分段
* 监视网络运行状况，不需要 SNMP

![NPM 拓扑图](./media/network-monitoring-overview/npm-topology-map.png) 

有关详细信息，请查看以下文章：

* [配置 Log Analytics 中的网络性能监视器解决方案](../log-analytics/log-analytics-network-performance-monitor.md) 
* [用例](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  产品更新：[2017 年 2 月](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)、[2017 年 8 月](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute 监视器

用于 ExpressRoute 的 NPM 针对专用的对等互连连接提供了全面的 ExpressRoute 监视。 可以通过 ExpressRoute 监视分支办事处与 Azure 之间的 E2E 连接和性能。 主要功能包括：

* 自动检测与你的订阅关联的 ER 线路
* 检测从本地到你的云应用程序的网络拓扑
* 容量规划，利用率分析
* 针对主要路径和辅助路径进行监视并发出警报
* 检测与 VNet 的连接的降级

有关详细信息，请参阅以下文章：

* [为 ExpressRoute 配置网络性能监视器](../expressroute/how-to-npm.md)
* [博客文章](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>服务终结点监视器

使用服务终结点监视功能，现在可以在本地、运营商网络和云/专用数据中心测试应用程序的可访问性并检测性能瓶颈。

* 监视到应用程序的端到端网络连接
* 将应用程序传送与网络性能相关联，检测用户与应用程序之间的路径上准确的降级位置
* 从全球的多个用户位置测试应用程序可访问性
* 确定你的业务线和 SaaS 应用程序的网络延迟和包丢失
* 确定网络上可能会导致应用程序性能变差的热点
* 使用针对 Microsoft Office 365、Dynamics 365、Skype for Business 和其他 Microsoft 服务的内置测试来监视 Office 365 应用程序的可访问性。

有关详细信息，请参阅以下文章：

* [配置用于监视服务终结点的网络性能监视器](https://aka.ms/applicationconnectivitymonitorguide)
* [博客文章](https://aka.ms/svcendptmonitor)

## <a name="next-steps"></a>后续步骤

* [配置网络性能监视器](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [为 ExpressRoute 配置网络性能监视器](../expressroute/how-to-npm.md)
