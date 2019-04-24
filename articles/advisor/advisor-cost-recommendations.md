---
title: 使用 Azure 顾问降低服务成本 | Microsoft Docs
description: 使用 Azure 顾问优化 Azure 部署的成本。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 188a79df99a174436808acd3d964abf9357cf4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467930"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>使用 Azure 顾问降低服务成本

通过识别闲置和未充分利用的资源，顾问有助于优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>通过调整或关闭未充分利用的实例来优化虚拟机花费 

虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 顾问可监视虚拟机 7 天的使用情况，并识别出利用率较低的虚拟机。 以下情况可以将虚拟机视为使用率低：其 CPU 使用率为 5% 或更低且其网络使用率低于 2%，或者更小的虚拟机大小可以容纳当前工作负荷。

顾问会显示继续运行虚拟机的预估成本，以便你选择关闭它还是对其进行调整。

若要加强对低使用率虚拟机的标识，可在每个订阅的基础上调整平均 CPU 使用率。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>通过消除未设置的 ExpressRoute 线路来降低成本

顾问将识别提供程序状态为“未设置”长达一个月以上的 ExpressRoute 线路将被顾问标识，如果没有使用连接性提供程序配置该线路的计划，顾问将建议删除它。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>通过删除或重新配置空闲虚拟网络网关来降低成本

顾问会识别已闲置超过 90 天的虚拟网络网关。 由于这些网关按小时计费，因此如果不打算再使用它们，则应考虑重新配置或删除它们。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>购买虚拟机预留实例可节省即用即付成本

顾问将查看过去 30 天的虚拟机使用情况，并确定是否可以通过购买 Azure 预留来节省资金。 顾问将展示可能在其中最大程度节省资金的区域和大小，并展示通过购买预留节约下来的估算费用。 通过 Azure 预留，可以预先购买虚拟机的基本成本。 折扣将自动应用于新的或现有的 VM，这些 VM 具有与预留相同的大小和区域。 [深入了解 Azure 虚拟机预留实例。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

顾问还会通知你的预订实例都可以将在接下来的 30 天后过期。 它将建议购买新的保留实例，以避免支付即用即付定价。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>删除未关联的公共 IP 地址可节省资金

顾问可以标识目前未关联到 Azure 资源（例如负载均衡器或 VM）的公共 IP 地址。 这些公共 IP 地址会产生少许费用。 如果不打算使用它们，删除它们可以节省成本。

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
