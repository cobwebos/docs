---
title: Azure 顾问成本建议 | Microsoft Docs
description: 使用 Azure 顾问优化 Azure 部署的成本。
services: advisor
documentationcenter: NA
author: kasparks
manager: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: a4de50e3dc17d9c9543bf1ec8cabb04c5d60070b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832881"
---
# <a name="advisor-cost-recommendations"></a>顾问成本建议

通过识别闲置和未充分利用的资源，顾问有助于优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>通过调整或关闭未充分利用的实例来优化虚拟机花费 
虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 顾问可监视虚拟机 14 天的使用情况，并识别出利用率较低的虚拟机。 如果在 4 天或 4 天以上，虚拟机的 CPU 利用率都小于或等于 5% 且网络使用率小于或等于 7 MB，则会被视为利用率较低的虚拟机。

顾问会显示继续运行虚拟机的预估成本，以便你选择关闭它还是对其进行调整。

若要加强对低使用率虚拟机的标识，可在每个订阅的基础上调整平均 CPU 使用率。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>通过消除未设置的 ExpressRoute 线路来降低成本
顾问将识别提供程序状态为“未设置”长达一个月以上的 ExpressRoute 线路将被顾问标识，如果没有使用连接性提供程序配置该线路的计划，顾问将建议删除它。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>通过删除或重新配置空闲虚拟网络网关来降低成本
顾问会识别已闲置超过 90 天的虚拟网络网关。 由于这些网关按小时计费，因此如果不打算再使用它们，则应考虑重新配置或删除它们。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>购买虚拟机预留实例可节省即用即付成本
顾问将查看过去 30 天的虚拟机使用情况，并确定是否可以通过购买 Azure 预留来节省资金。 顾问将展示可能在其中最大程度节省资金的区域和大小，并展示通过购买预留节约下来的估算费用。 

通过 Azure 预留，可以预先购买虚拟机的基本成本。 折扣将自动应用于新的或现有的 VM，这些 VM 具有与预留相同的大小和区域。 [深入了解 Azure 虚拟机预留实例。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

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
