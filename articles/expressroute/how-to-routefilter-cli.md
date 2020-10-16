---
title: 教程：配置用于 Microsoft 对等互连的路由筛选器 - Azure CLI
description: 本教程介绍如何使用 Azure CLI 配置用于 Microsoft 对等互连的路由筛选器。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975988"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>教程：配置用于 Microsoft 对等互连的路由筛选器：Azure CLI

> [!div class="op_single_selector"]
> * [Azure 门户](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

路由筛选器是通过 Microsoft 对等互连使用部分受支持服务的一种方法。 本文中的步骤可帮助配置和管理 ExpressRoute 线路的路由筛选器。

Microsoft 365 服务（例如 Exchange Online、SharePoint Online 和 Skype for Business）可通过 Microsoft 对等互连进行访问。 如果在 ExpressRoute 线路中配置了 Microsoft 对等互连，则会通过建立的 BGP 会话播发与这些服务相关的所有前缀。 每个前缀附加有 BGP 团体值，以标识通过该前缀提供的服务。 有关 BGP 团体值及其映射到的服务的列表，请参阅 [BGP 团体](expressroute-routing.md#bgp)。

与所有 Azure 和 Microsoft 365 服务的连接会导致大量前缀通过 BGP 进行播发。 大量前缀会大幅增加网络中路由器所维护路由表的大小。 如果打算仅使用通过 Microsoft 对等互连提供的一部分服务，可通过两种方式减少路由表大小。 方法：

* 通过在 BGP 团体上应用路由筛选器，筛选出不需要的前缀。 路由筛选是标准的网络做法，通常在多个网络中使用。

* 定义路由筛选器，并将其应用于 ExpressRoute 线路。 路由筛选器是一种新资源，可让你选择计划通过 Microsoft 对等互连使用的服务列表。 ExpressRoute 路由器仅发送属于路由筛选器中所标识服务的前缀列表。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 获取 BGP 社区值。
> - 创建路由筛选器和筛选器规则。
> - 将路由筛选器关联到 ExpressRoute 线路。

### <a name="about-route-filters"></a><a name="about"></a>关于路由筛选器

在 ExpressRoute 线路上配置 Microsoft 对等互连时，Microsoft 边缘路由器会通过连接提供商的边缘路由器建立一对 BGP 会话。 不会将任何路由播发到网络。 若要能够将路由播发到网络，必须关联路由筛选器。

使用路由筛选器可标识要通过 ExpressRoute 线路的 Microsoft 对等互连使用的服务。 它实质上是所有 BGP 社区值的允许列表。 定义路由筛选器资源并将其附加到 ExpressRoute 线路后，映射到 BGP 团体值的所有前缀均会播发到网络。

若要使用 Microsoft 365 服务附加路由筛选器，必须具备通过 ExpressRoute 使用 Microsoft 365 服务的权限。 如果未被授权通过 ExpressRoute 使用 Microsoft 365 服务，则附加路由筛选器的操作将失败。 若要深入了解授权过程，请参阅[适用于 Microsoft 365 的 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。
> 

## <a name="prerequisites"></a>先决条件

若要通过 Microsoft 对等互连成功连接服务，必须完成以下配置步骤：

* 必须具备预配了 Microsoft 对等互连的活动 ExpressRoute 线路。 可使用以下说明完成这些任务：
  * 继续之前，请[创建 ExpressRoute 线路](howto-circuit-cli.md)，并让连接提供商启用该线路。 ExpressRoute 线路必须处于已预配且已启用状态。
  * 如果直接管理 BGP 会话，请[创建 Microsoft 对等互连](howto-routing-cli.md)。 或者，让连接提供商为线路预配 Microsoft 对等互连。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装并使用 CLI，本快速入门要求 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅

要开始配置，请登录到 Azure 帐户。 如果选择“试用”，则会自动登录且可以跳过登录步骤。 使用下面的示例来帮助你连接：

```azurecli
az login
```

检查该帐户的订阅。

```azurecli-interactive
az account list
```

选择要为其创建 ExpressRoute 线路的订阅。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>获取前缀和 BGP 团体值的列表

1. 使用以下 cmdlet 获取与通过 Microsoft 对等互连可访问的服务相关联的 BGP 社区值和前缀列表：

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. 列出要在路由筛选器中使用的 BGP 团体值列表。

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>创建路由筛选器和筛选器规则

1 个路由筛选器只能有 1 个规则，并且规则类型必须是“允许”。 此规则可以有与之关联的 BGP 团体值列表。 命令 `az network route-filter create` 只创建路由筛选器资源。 创建资源后，必须创建规则并将其附加到路由筛选器对象。

1. 若要创建路由筛选器资源，请运行以下命令：

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. 若要创建路由筛选器规则，请运行以下命令：
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>将路由筛选器附加到 ExpressRoute 线路

运行以下命令将路由筛选器附加到 ExpressRoute 线路：

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>常见任务

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>获取路由筛选器的属性

若要获取路由筛选器的属性，请使用以下命令：

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>更新路由筛选器的属性

如果路由筛选器已附加到线路，则 BGP 社区列表的更新会通过已建立的 BGP 会话自动传播前缀播发更改。 可使用以下命令更新路由筛选器的 BGP 团体列表：

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>从 ExpressRoute 线路分离路由筛选器

从 ExpressRoute 线路分离路由筛选器后，BGP 会话不会播发任何前缀。 可使用以下命令从 ExpressRoute 线路分离路由筛选器：

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>清理资源

只有在路由筛选器未附加到任何线路时，才能将其删除。 尝试删除路由筛选器之前，请确保其未附加到任何线路。 可使用以下命令删除路由筛选器：

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

有关路由器配置示例的信息，请参阅：

> [!div class="nextstepaction"]
> [用于设置和管理路由的路由器配置示例](expressroute-config-samples-routing.md)
