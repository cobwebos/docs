---
title: 关于 Log Analytics 中的网络监视 | Microsoft Docs
description: 概述了网络监视解决方案，包括 NPM，它们用来在云、本地和混合环境中管理网络。
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 306d0e57449de41080d5473034e585f772771d51
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="network-monitoring-solutions"></a>网络监视解决方案 

Azure 提供了许多解决方案来监视网络资产。 Azure 具有相应的解决方案和实用程序来监视网络连接、ExpressRoute 线路的运行状况，以及分析云中的网络流量。

## <a name="network-performance-monitor-npm"></a>网络性能监视器 (NPM)

网络性能监视器 (NPM) 是一个功能套件，其中的每个功能分别用于监视网络的运行状况、应用程序的网络连接，以及深入了解网络性能。 NPM 基于云并提供了一个混合的网络监视解决方案，用于监视以下各项之间的连接：
 
* 云部署和本地位置
* 多个数据中心和分支机构
* 任务关键型多层应用程序/微服务
* 用户位置和基于 web 的应用程序 (HTTP/HTTPs) 

性能监视器、ExpressRoute 监视器和服务终结点监视器用于监视 NPM 中的功能，如下所述。

## <a name="performance-monitor"></a>性能监视

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
* 每个虚拟网络的容量规划、利用率分析和带宽利用率
* 针对主要路径和辅助路径进行监视并发出警报
* 检测与 VNet 的连接的降级

![显示各区域流量的地图](./media/network-monitoring-overview/expressroute-topology-map.png) 

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

## <a name="traffic-analytics"></a>流量分析
流量分析是一种基于云的解决方案，可查看云网络上的用户和应用程序活动。 分析 NSG 流日志可深入了解以下内容：

* Azure 和 Internet、公有云区域、VNET 和子网之间的网络上的通信流
* 网络上的应用程序和协议，而无需探测器或专用流收集器设备
* 最活跃的通信方、聊天应用程序、云中的 VM 对话、流量热点
* VNET 上的流量源和流量目标、关键业务服务和应用程序之间的相互关系
* 安全性 - 恶意流量、向 Internet 开放的端口、尝试访问 Internet 的应用程序或 VM...
* 容量使用量 - 通过监视 VPN 网关和其他服务的利用率趋势可帮助你消除过度预配或使用不足的问题

流量分析可以提供可操作的信息，帮助你审核组织的网络活动、保护应用程序和数据、优化工作负荷性能和保持符合性。

![显示各区域流量的地图](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

相关链接：
* [博客文章](https://aka.ms/trafficanalytics)、[文档](https://aka.ms/trafficanalyticsdocs)、[常见问题解答](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>DNS Analytics
此解决方案专为 DNS 管理员构建，可收集、分析和关联 DNS 日志，以提供与安全性、操作和性能相关的见解。  一些功能包括：

* 确定尝试解析恶意域的客户端
* 确定过时的资源记录
* 查看经常查询的域名和请求频繁的 DNS 客户端
* 查看 DNS 服务器上的请求负载
* 监视动态 DNS 注册错误

![DNS Analytics 仪表板](./media/network-monitoring-overview/dns-analytics-overview.png) 

相关链接：
* [博客文章](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/)、[文档](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>其他

* [新定价](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
