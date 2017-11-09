---
title: "配置用于 Azure ExpressRoute Microsoft 对等互连的路由筛选器：CLI | Microsoft Docs"
description: "本文介绍如何使用 Azure CLI 配置用于 Microsoft 对等互连的路由筛选器"
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>配置用于 Microsoft 对等互连的路由筛选器：Azure CLI

> [!div class="op_single_selector"]
> * [在 Azure 应用服务中创建 Java Web 应用](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由筛选器是通过 Microsoft 对等互连使用部分受支持服务的一种方法。 本文中的步骤可帮助配置和管理 ExpressRoute 线路的路由筛选器。

Dynamics 365 服务和 Office 365 服务（例如 Exchange Online、SharePoint Online 和 Skype for Business）均可通过 Microsoft 对等互连进行访问。 如果在 ExpressRoute 线路中配置 Microsoft 对等互连，则会通过建立的 BGP 会话播发与这些服务相关的所有前缀。 每个前缀附加有 BGP 团体值，以标识通过该前缀提供的服务。 有关 BGP 团体值及其映射到的服务的列表，请参阅 [BGP 团体](expressroute-routing.md#bgp)。

如需连接所有服务，则应通过 BGP 播发大量前缀。 这会显著增加网络中路由器所维护路由表的大小。 如果打算仅使用通过 Microsoft 对等互连提供的一部分服务，可通过两种方式减少路由表大小。 可以：

* 通过在 BGP 团体上应用路由筛选器，筛选出不需要的前缀。 这是标准的网络做法，通常在多个网络中使用。

* 定义路由筛选器，并将其应用于 ExpressRoute 线路。 路由筛选器是一种新资源，可让你选择计划通过 Microsoft 对等互连使用的服务列表。 ExpressRoute 路由器仅发送属于路由筛选器中所标识服务的前缀列表。

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

* 必须具备预配了 Microsoft 对等互连的活动 ExpressRoute 线路。 可使用以下说明完成这些任务：
  * 继续下一步之前，请[创建 ExpressRoute 线路](howto-circuit-cli.md)，并让连接提供商启用该线路。 ExpressRoute 线路必须处于已预配且已启用状态。
  * 如果直接管理 BGP 会话，请[创建 Microsoft 对等互连](howto-routing-cli.md)。 或者，让连接提供商为线路预配 Microsoft 对等互连。

* 必须创建并配置路由筛选器。
  * 标识要通过 Microsoft 对等互连使用的服务
  * 标识与服务关联的 BGP 团体值列表
  * 创建规则以允许前缀列表与 BGP 团体值相匹配

* 必须将路由筛选器附加到 ExpressRoute 线路。

## <a name="before-you-begin"></a>开始之前

在开始之前，请安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 和 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。

* 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。

* 必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](howto-circuit-cli.md) ，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配且已启用状态。

* 必须有活动的 Microsoft 对等互连。 按照[创建和修改对等互连配置](howto-routing-cli.md)中的说明操作

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅

要开始配置，请登录到 Azure 帐户。 使用以下示例来帮助连接：

```azurecli
az login
```

检查该帐户的订阅。

```azurecli
az account list
```

选择要为其创建 ExpressRoute 线路的订阅。

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>步骤 1：获取前缀和 BGP 社区值的列表

### <a name="1-get-a-list-of-bgp-community-values"></a>1.获取 BGP 团体值列表

使用以下 cmdlet 获取与通过 Microsoft 对等互连可访问服务相关联的 BGP 团体值列表，以及与之关联的前缀列表：

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2.列出要使用的值

列出要在路由筛选器中使用的 BGP 团体值列表。 例如，用于 Dynamics 365 服务的 BGP 团体值为 12076:5040。

## <a name="filter"></a>步骤 2：创建路由筛选器和筛选规则

1 个路由筛选器只能有 1 个规则，并且规则类型必须是“允许”。 此规则可以有与之关联的 BGP 团体值列表。

### <a name="1-create-a-route-filter"></a>1.创建路由筛选器

首先，创建路由筛选器。 命令“az network route-filter create”仅创建路由筛选器资源。 创建资源后，必须创建规则并将其附加到路由筛选器对象。 运行以下命令来创建路由筛选器资源：

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2.创建筛选器规则

运行以下命令来创建新规则：
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>步骤 3：将路由筛选器附加到 ExpressRoute 线路

运行以下命令将路由筛选器附加到 ExpressRoute 线路：

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>常见任务

### <a name="getproperties"></a>获取路由筛选器的属性

若要获取路由筛选器的属性，请使用以下命令：

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>更新路由筛选器的属性

如果路由筛选器已附加到线路，则 BGP 社区列表的更新会通过建立的 BGP 会话自动传播相应的前缀播发更改。 可使用以下命令更新路由筛选器的 BGP 团体列表：

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>从 ExpressRoute 线路分离路由筛选器

从 ExpressRoute 线路分离路由筛选器后，BGP 会话不会播发任何前缀。 可使用以下命令从 ExpressRoute 线路分离路由筛选器：

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>删除路由筛选器

只有在路由筛选器未附加到任何线路时，才能将其删除。 尝试删除路由筛选器之前，请确保其未附加到任何线路。 可使用以下命令删除路由筛选器：

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
