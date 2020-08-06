---
title: 使用 Azure Monitor Insights 监视 Azure 虚拟 WAN
description: 了解如何使用 Azure Monitor Insights 监视虚拟 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836065"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>虚拟 WAN (预览版 Azure Monitor 见解) 

[Azure Monitor](../azure-monitor/insights/network-insights-overview.md)的虚拟 wan Insights 使用户和操作员能够查看虚拟 wan 的状态和状态，通过 autodiscovered 拓扑映射显示。 资源状态和状态在映射上重叠，为你显示了虚拟 WAN 总体运行状况的快照视图。 通过单击 "虚拟 WAN 门户" 的 "资源配置" 页上的 "访问" 来在地图上启用资源导航。

虚拟 WAN 资源级别度量值是通过预打包的虚拟 WAN 指标工作簿收集和呈现的，该工作簿显示虚拟 WAN、集线器、网关和连接级别的指标。 本文将指导你完成使用 Azure Monitor Insights 进行虚拟广域网的步骤，以在单个位置查看虚拟 WAN 拓扑和指标。

> [!NOTE]
> 虚拟 WAN 门户中的 "见解" 菜单选项正在推出。当推出虚拟 WAN 的 Insights 菜单时，可以使用 Azure 监视器 for 网络直接访问虚拟 WAN 拓扑和指标工作簿。 有关详细信息，请参阅[Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md) 。 
>

## <a name="before-you-begin"></a>开始之前

本文中的步骤假设你已部署了具有一个或多个中心的虚拟 WAN。 若要创建新虚拟 WAN 和新中心，请使用以下文章中的步骤：

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [创建中心](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>查看 VWAN 拓扑

在**Azure 门户 >虚拟 WAN**中，从左侧的 "**监视**" 菜单中选择 " **Insights (预览") **。 这会显示 " **Insights" 视图**，其中显示了虚拟 WAN 依赖关系映射和高级指标最小工作簿。

**图1：监视-Insights 菜单**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="算" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

在 "**见解**" 视图中，可以查看端到端虚拟 wan 中的 AUTODISCOVERED 虚拟 wan 资源，如集线器、网关、防火墙、连接和辐射 vnet、第三方 nva 和分支，如**图 2**所示。

**资源状态**和**状态**以颜色编码，并叠加在映射中的资源图标上。 虚拟 WAN 高级指标（如集线器容量和网关利用率）通过微型工作簿显示在右侧。

**图2： Insights 视图**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="算" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>“依赖项”视图

虚拟 WAN 的**依赖关系**视图可直观显示广泛组织到中心辐射型体系结构中的所有虚拟 WAN 资源的互连视图。

**图3： VWAN 依赖关系视图**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="依赖关系映射" lightbox="./media/azure-monitor-insights/dependency-map.png":::

依赖关系视图映射将以下资源显示为连接的图形：

* 跨各种 Azure 区域的虚拟 WAN 中心。
* 直接连接到中心的分支 Vnet。
* 通过各自的 ExpressRoute、S2S 和 P2S 连接以及虚拟网络网关连接到每个集线器的 VPN 和 ExpressRoute 分支站点和 P2S 用户。
* Azure 防火墙 (包括) 部署在中心 (安全中心) 的第三方防火墙代理。
* 第三方 NVA 在分支 Vnet 中部署 (网络虚拟设备) 。

依赖关系图还显示与虚拟 WAN 辐射 Vnet) 对等互连的间接连接的 Vnet (VNet。

依赖关系图可轻松地导航到每个资源的配置设置。 例如，你可以将鼠标悬停在中心资源上，以查看基本资源配置，如中心区域和中心前缀。 右键单击以访问中心资源的 "Azure 门户" 页。

**图4：导航到特定于资源的信息**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="资源信息":::

"依赖关系" 视图上的搜索和筛选栏提供了一种简便的方式来搜索图形。 不同的筛选器可帮助将搜索范围缩小到特定路径和状态。

**图5：搜索和筛选**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="搜索和筛选栏" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>详细度量值

你可以选择 "**查看详细度量值**" 来访问 "详细**指标**" 页。 "指标" 页是预先配置的仪表板，其中包含单独的选项卡，可用于在虚拟 WAN 级别、中心级别和单个连接上深入了解虚拟 WAN 资源容量、性能和利用率。

**图6：详细度量值仪表板**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="详细度量值" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>后续步骤

* 若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](../azure-monitor/platform/data-platform-metrics.md)。
* 有关所有虚拟 WAN 指标的完整说明，请参阅[虚拟 wan 日志和指标](logs-metrics.md)。
