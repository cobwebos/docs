---
title: "配置用于 Azure ExpressRoute Microsoft 对等互连的路由筛选器：门户 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户配置用于 Microsoft 对等互连的路由筛选器"
documentationcenter: na
services: expressroute
author: ganesr
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: ganesr
ms.openlocfilehash: 0129a48e43e90001785a5977d4b0d1fd9fa9fd7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>配置用于 Microsoft 对等互连的路由筛选器：Azure 门户
> [!div class="op_single_selector"]
> * [在 Azure 应用服务中创建 Java Web 应用](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由筛选器是通过 Microsoft 对等互连使用部分受支持服务的一种方法。 本文中的步骤可帮助配置和管理 ExpressRoute 线路的路由筛选器。

Dynamics 365 服务、Office 365 服务（例如 Exchange Online、SharePoint Online 和 Skype for Business）及 Azure 服务（例如存储和 SQL DB）均可通过 Microsoft 对等互连进行访问。 如果在 ExpressRoute 线路中配置 Microsoft 对等互连，则会通过建立的 BGP 会话播发与这些服务相关的所有前缀。 每个前缀附加有 BGP 团体值，以标识通过该前缀提供的服务。 有关 BGP 团体值及其映射到的服务的列表，请参阅 [BGP 团体](expressroute-routing.md#bgp)。

如需连接所有服务，则应通过 BGP 播发大量前缀。 这会显著增加网络中路由器所维护路由表的大小。 如果打算仅使用通过 Microsoft 对等互连提供的一部分服务，可通过两种方式减少路由表大小。 可以：

- 通过在 BGP 团体上应用路由筛选器，筛选出不需要的前缀。 这是标准的网络做法，通常在多个网络中使用。

- 定义路由筛选器，并将其应用于 ExpressRoute 线路。 路由筛选器是一种新资源，可让你选择计划通过 Microsoft 对等互连使用的服务列表。 ExpressRoute 路由器仅发送属于路由筛选器中所标识服务的前缀列表。

### <a name="about"></a>关于路由筛选器

在 ExpressRoute 线路上配置 Microsoft 对等互连时，Microsoft 边缘路由器会建立你的或你连接提供商的边缘路由器的一对 BGP 会话。 不会将任何路由播发到网络。 若要能够将路由播发到网络，必须关联路由筛选器。

使用路由筛选器可标识要通过 ExpressRoute 线路的 Microsoft 对等互连使用的服务。 它实质上是所有 BGP 团体值的允许列表。 定义路由筛选器资源并将其附加到 ExpressRoute 线路后，映射到 BGP 团体值的所有前缀均会播发到网络。

为了能够将 Office 365 服务的路由筛选器附加到线路，必须具备通过 ExpressRoute 使用 Office 365 服务的权限。 如果未被授权通过 ExpressRoute 使用 Office 365 服务，则附加路由筛选器的操作将失败。 若要深入了解授权过程，请参阅[适用于 Office 365 的 Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)。 连接 Dynamics 365 服务不需要任何事先授权。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。
> 
> 

### <a name="workflow"></a>工作流

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


## <a name="prefixes"></a>步骤 1：获取前缀和 BGP 团体值的列表

### <a name="1-get-a-list-of-bgp-community-values"></a>1.获取 BGP 团体值列表

可从 [ExpressRoute 路由要求](expressroute-routing.md)页获取与可通过 Microsoft 对等互连访问的服务关联的 BGP 社区值。

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2.列出要使用的值

列出要在路由筛选器中使用的 BGP 团体值列表。 例如，用于 Dynamics 365 服务的 BGP 团体值为 12076:5040。

## <a name="filter"></a>步骤 2：创建路由筛选器和筛选规则

1 个路由筛选器只能有 1 个规则，并且规则类型必须是“允许”。 此规则可以有与之关联的 BGP 团体值列表。

### <a name="1-create-a-route-filter"></a>1.创建路由筛选器
可以通过选择创建新资源的选项来创建路由筛选器。 单击“新建” > “网络” > “RouteFilter”，如下图所示：

![创建路由筛选器](.\media\how-to-routefilter-portal\CreateRouteFilter1.png)

必须将路由筛选器放置在资源组中。 

![创建路由筛选器](.\media\how-to-routefilter-portal\CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2.创建筛选器规则

可通过选择管理规则选项卡添加和更新路由筛选器规则。

![创建路由筛选器](.\media\how-to-routefilter-portal\ManageRouteFilter.png)


可从下拉列表中选择希望连接的服务，并在完成后保存规则。

![创建路由筛选器](.\media\how-to-routefilter-portal\AddRouteFilterRule.png)


## <a name="attach"></a>步骤 3：将路由筛选器附加到 ExpressRoute 线路

可通过选择“添加电路”按钮并从下拉列表中选择 ExpressRoute 线路将路由筛选器附加到线路。

![创建路由筛选器](.\media\how-to-routefilter-portal\AddCktToRouteFilter.png)

如果连接服务提供商为 ExpressRoute 线路配置了对等互连，请先从 ExpressRoute 线路边栏选项卡中刷新线路，再选择“添加线路”按钮。

![创建路由筛选器](.\media\how-to-routefilter-portal\RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>常见任务

### <a name="getproperties"></a>获取路由筛选器的属性

在门户中打开资源时，可以查看路由筛选器的属性。

![创建路由筛选器](.\media\how-to-routefilter-portal\ViewRouteFilter.png)


### <a name="updateproperties"></a>更新路由筛选器的属性

可通过选择“管理规则”按钮更新附加到线路的 BGP 社区值列表。


![创建路由筛选器](.\media\how-to-routefilter-portal\ManageRouteFilter.png)

![创建路由筛选器](.\media\how-to-routefilter-portal\AddRouteFilterRule.png) 


### <a name="detach"></a>从 ExpressRoute 线路分离路由筛选器

若要从路由筛选器中分离线路，请右键单击线路并单击“取消关联”。

![创建路由筛选器](.\media\how-to-routefilter-portal\DetachRouteFilter.png) 


### <a name="delete"></a>删除路由筛选器

可通过选择“删除”按钮删除路由筛选器。 

![创建路由筛选器](.\media\how-to-routefilter-portal\DeleteRouteFilter.png) 

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。