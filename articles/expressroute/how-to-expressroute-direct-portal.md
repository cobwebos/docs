---
title: Azure ExpressRoute：配置 ExpressRoute Direct：门户
description: 此页可帮助你使用门户配置 ExpressRoute 直接连接。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: c4021fbf87cc7cff8dde8e759423eb52c705cf97
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568329"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>使用 Azure 门户创建 ExpressRoute 直接

本文介绍如何使用 Azure 门户创建 ExpressRoute 直接连接。
通过 ExpressRoute Direct，你可以直接连接到在世界上战略分散的对等互连位置上的 Microsoft 全球网络。 有关详细信息，请参阅[关于 ExpressRoute Direct](expressroute-erdirect-about.md)。

## <a name="before-you-begin"></a><a name="before"></a>准备工作

验证是否已将 **Microsoft 网络** 资源提供程序注册到你的订阅。 通过注册资源提供程序来配置订阅，以供资源提供程序使用。

1. 如 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)中所述，访问你的订阅设置。
1. 在订阅中，为 **资源提供程序**验证 **Microsoft 网络** 提供程序是否显示 **已注册** 状态。 如果已注册的提供程序的列表中不存在 Microsoft. 网络资源提供程序，请添加该提供程序。

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>创建 ExpressRoute 直接

1. 在 " [Azure 门户](https://portal.azure.com) " 菜单中，或从 **主页** 中选择 " **创建资源**"。

1. 在 " **新建** " 页上的 " ***搜索 Marketplace"*** 字段中，键入 **ExpressRoute Direct**，然后选择 **Enter** 以转到搜索结果。

1. 从结果中选择 " **ExpressRoute 直接**"。

1. 在 **ExpressRoute 直接** 页面上，选择 " **创建** " 以打开 " **创建 ExpressRoute 直接** " 页。

1. 首先完成 " **基本** 信息" 页上的字段。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="“基本信息”页":::

    * **订阅**：要用来创建新 ExpressRoute 直接的 Azure 订阅。 ExpressRoute Direct 资源和 ExpressRoute 线路必须位于同一订阅中。
    * **资源组**：要在其中创建新的 ExpressRoute 直接资源的 Azure 资源组。 如果目前没有资源组，可新建一个。
    * **区域**：将在其中创建资源的 Azure 公共区域。
    * **名称**：新的 ExpressRoute 直接资源的名称。

1. 接下来，完成 **配置** 页上的字段。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="“基本信息”页" 来验证 ExpressRoute 直接资源设置。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="“基本信息”页":::

1. 选择“创建”。 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a> (LOA 生成授权信) 

此时无法从门户生成授权信。 使用 **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** 或 **[Azure CLI](expressroute-howto-expressroute-direct-cli.md#authorization)** 获取授权信。

## <a name="change-admin-state-of-links"></a><a name="state"></a>更改链路的管理状态

应当使用此过程执行第 1 层测试，以确保每个交叉连接都已针对主端口和辅助端口正确设置到每台路由器中。

1. 在 "ExpressRoute 直接资源 **概述** " 页上的 " **链接** " 部分中，选择 " **link1**"。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="“基本信息”页" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. 将 **管理员状态** 设置切换为 " **已启用**"，然后选择 " **保存**"。

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="“基本信息”页":::

    >[!IMPORTANT]
    >在任一链接上启用管理状态后，将开始计费。
    >

1. 对 **link2**重复相同的过程。

## <a name="create-a-circuit"></a><a name="circuit"></a>创建线路

默认情况下，可以在 ExpressRoute Direct 资源所在的订阅中创建 10 条线路。 此数字可以通过支持来提高。 你负责跟踪预配的和已利用的带宽。 预配的带宽是 ExpressRoute 直接资源上所有线路的带宽之和。 利用带宽是基础物理接口的物理用法。

* 有额外的线路带宽可以在 ExpressRoute Direct 上使用，仅用于支持上面概述的场景。 它们是： 40 Gbps 和 100 Gbps。

* SkuTier 可以是本地、标准或高级。

* SkuFamily 必须为 MeteredData。 ExpressRoute 直接不支持无限制。

以下步骤可帮助你通过 ExpressRoute 直接工作流创建 ExpressRoute 线路。 如果您愿意，也可以使用常规线路工作流创建线路，不过，使用此配置的常规电路工作流步骤并不是很有好处。 请参阅 [创建和修改 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)。

1. 在 "ExpressRoute 直接 **设置** " 部分中，选择 " **线路**"，然后选择 " **+ 添加**"。 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="“基本信息”页" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. 配置 " **配置** " 页中的设置。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="“基本信息”页":::

1. 指定任何资源标记，即 "选择 **评审 + 创建** "，以便在创建资源之前验证这些值。

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="“基本信息”页":::

1. 选择“创建”。 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute Direct 的详细信息，请参阅 [概述](expressroute-erdirect-about.md)。
