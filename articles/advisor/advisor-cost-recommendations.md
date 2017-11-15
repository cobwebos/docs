---
title: "Azure 顾问成本建议 | Microsoft Docs"
description: "使用 Azure 顾问优化 Azure 部署的成本。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>顾问成本建议

通过识别闲置和未充分利用的资源，顾问有助于优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>通过调整或关闭未充分利用的实例来优化虚拟机花费 
虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 顾问可监视虚拟机 14 天的使用情况，并识别出利用率较低的虚拟机。 如果在 4 天或 4 天以上，虚拟机的 CPU 利用率都小于或等于 5% 且网络使用率小于或等于 7 MB，则会被视为利用率较低的虚拟机。

顾问会显示继续运行虚拟机的预估成本，以便你选择关闭它还是对其进行调整。

若要加强对低使用率虚拟机的标识，可在每个订阅的基础上调整平均 CPU 使用率。

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>使用经济高效的解决方案管理多个 SQL 数据库的性能目标
顾问可识别可从创建弹性数据库池受益的 SQL server 实例。 弹性数据库池是一种简单的低成本高效益的解决方案，用于管理多个数据库的性能目标，其使用模式变化很大。 有关 Azure 弹性数据库池的详细信息，请参阅[什么是 Azure 弹性池？](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>通过消除未设置的 ExpressRoute 线路来降低成本
顾问将识别提供程序状态为“未设置”长达一个月以上的 ExpressRoute 线路将被顾问标识，如果没有使用连接性提供程序配置该线路的计划，顾问将建议删除它。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的成本建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“成本”选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问性能建议](advisor-cost-recommendations.md)
* [顾问高可用性建议](advisor-cost-recommendations.md)
* [顾问安全性建议](advisor-cost-recommendations.md)
