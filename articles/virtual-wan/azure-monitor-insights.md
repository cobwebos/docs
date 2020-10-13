---
title: 使用 Azure Monitor Insights 监视虚拟 WAN
description: 本文介绍如何使用 Azure Monitor Insights 来监视 Azure 虚拟 WAN。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 052fd0ea7619d566e78806580ee7b39e49cc85d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448613"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>虚拟 WAN (预览版 Azure Monitor 见解) 

Azure 虚拟 WAN [Azure Monitor Insights](../azure-monitor/insights/network-insights-overview.md)使用户和操作员能够查看虚拟 WAN 的状态和状态，这些信息通过 autodiscovered 拓扑映射显示。 地图上的资源状态和状态重叠为你显示了虚拟 WAN 总体运行状况的快照视图。 可以通过一次单击访问虚拟 WAN 门户的资源配置页，在地图上导航资源。

虚拟 WAN 资源级指标通过预打包的虚拟 WAN 指标工作簿进行收集和显示。 工作簿显示虚拟 WAN、集线器、网关和连接级别的指标。 本文将指导你完成使用 Azure Monitor Insights 进行虚拟广域网的步骤，以在单个位置查看虚拟 WAN 拓扑和指标。

> [!NOTE]
> 虚拟 WAN 门户中的 " **Insights** " 菜单选项目前正在推出。在此菜单推出时，可以使用网络 Azure Monitor 访问虚拟 WAN 拓扑和指标工作簿。 有关详细信息，请参阅 [网络 Azure Monitor](../azure-monitor/insights/network-insights-overview.md)。 
>

## <a name="before-you-begin"></a>在开始之前

若要完成本文中的步骤，需要具有一个或多个集线器的虚拟 WAN。 若要创建虚拟 WAN 和中心，请遵循以下文章中的步骤：

* [创建虚拟 WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [创建中心](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>查看 VWAN 拓扑

请参阅**Azure 门户**  >  **虚拟 WAN**。 在左侧窗格的 " **监视** " 菜单中，选择 " **Insights (预览") **。 **Insights**视图随即出现。 其中显示了虚拟 WAN 依赖关系映射和高级别 **指标** 小型工作簿。

**图1：监视器 > Insights 菜单**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="显示见解 (预览) 视图的屏幕截图。" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

在 " **见解** " 视图中，可以查看 AUTODISCOVERED 虚拟 WAN 资源。 这些资源包括：集线器、网关、防火墙、连接和辐射虚拟网络、第三方 Nva，以及端到端虚拟 WAN 中的分支。 有关示例，请参见 **图 2**。

资源状态和状态以颜色编码，并叠加在映射中的资源图标上。 高级虚拟 WAN 指标（如集线器容量和网关利用率）显示在小型工作簿中窗口的右侧。

**图2： Insights 视图**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="显示见解 (预览) 视图的屏幕截图。" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>“依赖项”视图

虚拟 WAN 的 **依赖关系** 视图可帮助你直观地显示广泛组织到中心辐射型体系结构中的所有虚拟 WAN 资源的相互连接视图。

**图3： VWAN 依赖关系视图**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="显示见解 (预览) 视图的屏幕截图。" lightbox="./media/azure-monitor-insights/dependency-map.png":::

**依赖关系**视图映射将以下资源显示为连接的图形：

* 跨各种 Azure 区域的虚拟 WAN 中心。
* 直接连接到中心的辐射虚拟网络。
* 通过各自的 ExpressRoute、S2S 和 P2S 连接和虚拟网络网关连接到每个集线器的 VPN 和 Azure ExpressRoute 分支站点和 P2S 用户。
* Azure 防火墙 (包括) 部署在中心 (安全中心) 的第三方防火墙代理。
* 第三方 Nva (在辐射虚拟网络中部署) 网络虚拟设备。

依赖关系图还显示 (虚拟网络的间接连接的虚拟网络，这些网络与虚拟 WAN 辐射虚拟网络) 对等互连。

依赖关系图可轻松地导航到每个资源的配置设置。 例如，你可以将鼠标悬停在中心资源上，以查看基本资源配置，如中心区域和中心前缀。 右键单击以访问中心资源的 "Azure 门户" 页。

**图4：导航到特定于资源的信息**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="显示见解 (预览) 视图的屏幕截图。" 视图中的搜索和筛选栏提供了一种简单的方法来在图形上进行搜索。 各种筛选器可帮助将搜索范围缩小到特定路径和状态。

**图5：搜索和筛选**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="显示见解 (预览) 视图的屏幕截图。" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>详细度量值

你可以选择 " **查看详细度量值** " 来访问 "详细 **指标** " 页。 " **指标** " 页是使用单独的选项卡预先配置的仪表板。 这些选项卡提供了虚拟 wan 资源容量、性能和虚拟广域网级别和中心级别的利用率，以及各个连接的级别。

**图6：详细度量值仪表板**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="显示见解 (预览) 视图的屏幕截图。" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>后续步骤

* 若要了解详细信息，请参阅 [Azure Monitor 中的度量值](../azure-monitor/platform/data-platform-metrics.md)。
* 有关所有虚拟 WAN 指标的完整说明，请参阅 [虚拟 wan 日志和指标](logs-metrics.md)。
