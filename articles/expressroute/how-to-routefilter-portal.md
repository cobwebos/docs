---
title: ExpressRoute：路由筛选器-Microsoft 对等互连： Azure 门户
description: 本文介绍如何使用 Azure 门户配置用于 Microsoft 对等互连的路由筛选器。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 468c7a0113a4603f4f47bb529145261ff50d96d4
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650538"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>配置用于 Microsoft 对等互连的路由筛选器：Azure 门户
> [!div class="op_single_selector"]
> * [Azure 门户](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由筛选器是通过 Microsoft 对等互连使用部分受支持服务的一种方法。 本文中的步骤可帮助配置和管理 ExpressRoute 线路的路由筛选器。

可以通过 Microsoft 对等互连访问 Microsoft 365 服务（如 Exchange Online、SharePoint Online 和 Skype for Business）以及 Azure 服务（如存储和 SQL 数据库）。 如果在 ExpressRoute 线路中配置 Microsoft 对等互连，则会通过建立的 BGP 会话播发与这些服务相关的所有前缀。 每个前缀附加有 BGP 团体值，以标识通过该前缀提供的服务。 有关 BGP 团体值及其映射到的服务的列表，请参阅 [BGP 团体](expressroute-routing.md#bgp)。

如需连接所有服务，则应通过 BGP 播发大量前缀。 这会显著增加网络中路由器所维护路由表的大小。 如果打算仅使用通过 Microsoft 对等互连提供的一部分服务，可通过两种方式减少路由表大小。 您可以：

- 通过在 BGP 团体上应用路由筛选器，筛选出不需要的前缀。 这是标准的网络做法，通常在多个网络中使用。

- 定义路由筛选器，并将其应用于 ExpressRoute 线路。 路由筛选器是一种新资源，可让你选择计划通过 Microsoft 对等互连使用的服务列表。 ExpressRoute 路由器仅发送属于路由筛选器中所标识服务的前缀列表。

### <a name="about-route-filters"></a><a name="about"></a>关于路由筛选器

在 ExpressRoute 线路上配置 Microsoft 对等互连时，Microsoft 边缘路由器会建立你的或你连接提供商的边缘路由器的一对 BGP 会话。 不会将任何路由播发到网络。 若要能够将路由播发到网络，必须关联路由筛选器。

使用路由筛选器可标识要通过 ExpressRoute 线路的 Microsoft 对等互连使用的服务。 它实质上是要允许的所有 BGP 社区值的列表。 定义路由筛选器资源并将其附加到 ExpressRoute 线路后，映射到 BGP 团体值的所有前缀均会播发到网络。

若要能够将路由筛选器附加到其上的 Microsoft 365 服务，必须通过 ExpressRoute 使用 Microsoft 365 服务的授权。 如果无权通过 ExpressRoute 使用 Microsoft 365 服务，则附加路由筛选器的操作将失败。 有关授权过程的详细信息，请参阅 [适用于 Microsoft 365 的 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。
> 
> 

### <a name="workflow"></a><a name="workflow"></a>工作流

若要通过 Microsoft 对等互连成功连接服务，必须完成以下配置步骤：

- 必须具备预配了 Microsoft 对等互连的活动 ExpressRoute 线路。 可使用以下说明完成这些任务：
  - 继续下一步之前，请[创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)，并让连接提供商启用该线路。 ExpressRoute 线路必须处于已预配且已启用状态。
  - 如果直接管理 BGP 会话，请[创建 Microsoft 对等互连](expressroute-howto-routing-portal-resource-manager.md)。 或者，让连接提供商为线路预配 Microsoft 对等互连。

-  必须创建并配置路由筛选器。
    - 标识要通过 Microsoft 对等互连使用的服务
    - 标识与服务关联的 BGP 团体值列表
    - 创建规则以允许前缀列表与 BGP 团体值相匹配

-  必须将路由筛选器附加到 ExpressRoute 线路。

## <a name="before-you-begin"></a>开始之前

开始配置之前，请确保满足以下条件：

 - 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。

 - 必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md) ，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配且已启用状态。

 - 必须有活动的 Microsoft 对等互连。 按照[创建和修改对等互连配置](expressroute-howto-routing-portal-resource-manager.md)中的说明操作


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>步骤 1：获取前缀和 BGP 团体值的列表

### <a name="1-get-a-list-of-bgp-community-values"></a>1. 获取 BGP 团体值的列表

可从 [ExpressRoute 路由要求](expressroute-routing.md)页获取与可通过 Microsoft 对等互连访问的服务关联的 BGP 社区值。

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. 创建要使用的值的列表

创建要在路由筛选器中使用的 [BGP 团体值](expressroute-routing.md#bgp) 列表。 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>步骤 2：创建路由筛选器和筛选规则

1 个路由筛选器只能有 1 个规则，并且规则类型必须是“允许”。 此规则可以有与之关联的 BGP 团体值列表。

### <a name="1-create-a-route-filter"></a>1. 创建路由筛选器
可以通过选择创建新资源的选项来创建路由筛选器。 单击 "**创建资源**  >  **Networking**  >  " "网络" "**RouteFilter**"，如下图所示：

![显示 "路由筛选器" 页的屏幕截图。](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

必须将路由筛选器放置在资源组中。 

![屏幕截图显示 "创建路由筛选器" 页，其中包含输入的示例值。](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. 创建筛选规则

可通过选择管理规则选项卡添加和更新路由筛选器规则。

![显示 "概述" 页面并突出显示 "管理规则" 操作的屏幕截图。](./media/how-to-routefilter-portal/ManageRouteFilter.png)


可从下拉列表中选择希望连接的服务，并在完成后保存规则。

![显示 "管理规则" 窗口的屏幕截图，其中选择了 "允许的服务社区" 下拉列表中的服务。](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>步骤 3：将路由筛选器附加到 ExpressRoute 线路

可通过选择“添加线路”按钮并从下拉列表中选择 ExpressRoute 线路将路由筛选器附加到线路。

![显示 "概述" 页并选择 "添加线路" 操作的屏幕截图。](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

如果连接服务提供商为 ExpressRoute 线路配置了对等互连，请先从 ExpressRoute 线路边栏选项卡中刷新线路，再选择“添加线路”按钮。

![显示 "概述" 页并选择 "刷新" 操作的屏幕截图。](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>常见任务

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>获取路由筛选器的属性

在门户中打开资源时，可以查看路由筛选器的属性。

![显示 "概述" 页的屏幕截图。](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>更新路由筛选器的属性

可通过选择“管理规则”按钮更新附加到线路的 BGP 社区值列表。


![显示 "概述" 页面并突出显示 "管理规则" 操作的屏幕截图。](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![显示选择了服务的 "管理规则" 窗口的屏幕截图。](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>从 ExpressRoute 线路分离路由筛选器

若要从路由筛选器中分离线路，请右键单击线路并单击“取消关联”。

![显示 "概述" 页的屏幕截图，其中突出显示了 "取消关联" 操作。](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>删除路由筛选器

可通过选择“删除”按钮删除路由筛选器。 

![创建路由筛选器](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>后续步骤

* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

* 有关路由器配置示例的信息，请参阅[设置和管理路由的路由器配置示例](expressroute-config-samples-routing.md)。 
