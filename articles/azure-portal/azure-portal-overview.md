---
title: Azure 门户概述 | Microsoft Docs
description: Azure 门户是可用于管理 Azure 服务的图形用户界面。 了解如何在 Azure 门户中导航和查找资源。
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: overview
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 4aa1ccbd9b605850052e190949542eb014fe90a8
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763918"
---
# <a name="azure-portal-overview"></a>Azure 门户概述

本文介绍 Azure 门户、标识门户页面元素，并帮助你熟悉 Azure 门户管理体验。

## <a name="what-is-the-azure-portal"></a>什么是 Azure 门户？

Azure 门户是一种基于 Web 的统一控制台，它提供了命令行工具的替代方法。 通过 Azure 门户，可以使用图形用户界面管理 Azure 订阅。 可以生成、管理和监视一切内容，范围从简单的 Web 应用到复杂的云部署不等。 为井然有序的资源视图创建自定义仪表板。 配置辅助功能选项以获得最佳体验。

Azure 门户旨在实现复原能力和持续可用性。 它存在于每个 Azure 数据中心中。 此配置使 Azure 门户能够从单独的数据中心故障中恢复，并通过接近用户来避免网络变慢。 Azure 门户会持续更新，并且无需停机进行维护活动。

## <a name="azure-portal-menu"></a>Azure 门户菜单

可以选择门户菜单的默认模式。 它可以停靠，也可以充当弹出面板。

门户菜单处于弹出模式时，它将隐藏起来，直到你需要它。 选择菜单图标以打开或关闭菜单。

![弹出模式下的 Azure 门户菜单](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

如果为门户菜单选择停靠模式，它将始终可见。 可以折叠菜单以提供更多工作空间。

![停靠模式下的 Azure 门户菜单](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure 主页

作为 Azure 服务的新订阅者，你在[登录到门户](https://portal.azure.com)后，首先看到的内容是“Azure 主页”。 此页面会编译资源，帮助充分利用 Azure 订阅。 我们已包含免费在线课程、文档、核心服务和有用网站的链接，可让你保持最新状态并管理贵组织的变更。 为了让你快速、轻松地访问正在进行的工作，我们还会显示你最近访问过的资源列表。 你无法自定义此页面，但可以选择是将“Azure 主页”还是“Azure 仪表板”作为默认视图 。 首次登录时，页面顶部会出现提示，可在此处保存你的首选项。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-default-view.png)

可以在“门户设置”中更改 Azure 门户菜单和 Azure 默认视图。 如果更改选择项，将立即应用更改。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure 仪表板

仪表板提供订阅中最至关重要的资源的集中视图。 我们提供一个默认仪表板来帮助你入门。 可以自定义此仪表板，将经常使用的资源引入单个视图。 对默认视图所做的任何更改仅影响你的体验。 但是，你可以创建其他仪表板供自己使用，也可以发布自定义仪表板，并与组织中的其他用户共享这些仪表板。 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>浏览门户

了解基本门户布局以及其交互方式会很有用。 在这里，我们将介绍用户界面的组件以及我们用于说明的一些术语。 如需详细了解门户概览，请参阅课程[导航到门户](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)。

Azure 门户菜单和页眉是始终存在的全局元素。 这些持久功能是与每个单独服务或功能关联的用户界面的“外壳”，而标头可提供对全局控件的访问。 资源的配置页（有时称为“边栏选项卡”）可能还有一个资源菜单，可帮助你在功能之间移动。

下图标记了 Azure 门户的基本元素，而每个元素都在下表中进行了说明。

![显示全屏门户视图和 UI 元素项的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![显示扩展门户菜单的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|密钥|说明
|:---:|---|
|1|页眉。 出现在每个门户页面的顶部，并保存全局元素。|
|2| 全局搜索。 使用搜索栏快速查找特定资源、服务或文档。|
|3|全局控件。 与所有全局元素一样，这些功能会在整个门户中保留，包括：Cloud Shell、订阅筛选器、通知、门户设置、帮助和支持，以及向我们发送反馈。|
|4|你的帐户。 查看有关你的帐户的信息、切换目录、注销或使用其他帐户登录。|
|5|门户菜单。 门户菜单是一个全局元素，可帮助你在服务之间导航。 有时也称为边栏，可在“门户设置”中更改门户菜单模式。|
|6|资源菜单。 许多服务都包括一个资源菜单，以帮助你管理服务。 你可能会看到此元素称为左窗格。|
|7|命令栏。 命令栏上的控件与当前的焦点相关。|
|8|工作窗格。  显示有关当前焦点所在资源的详细信息。|
|9|痕迹导航。 可以使用痕迹导航链接在工作流中返回一个级别。|
|10|用于在当前订阅中创建新资源的主控件。 展开或打开门户菜单，查找“+ 创建资源”。 搜索或浏览 Azure 市场以获取要创建的资源类型。|
|11|收藏夹列表。 有关如何自定义列表的信息，请参阅[添加、删除和排序收藏夹](../azure-portal/azure-portal-add-remove-sort-favorites.md)。|

## <a name="get-started-with-services"></a>服务入门

如果你是新订阅者，则必须先创建资源，才能进行管理。 选择“+ 创建资源”以查看 Azure 市场中可用的服务。 你将在此处找到来自数百家提供商的应用程序和服务，所有内容都经过 Azure 认证。

我们预先填充了边栏中的收藏夹与常用服务的链接。  要查看所有可用服务，请从边栏选择“所有服务”。

> [!TIP]
> 查找资源、服务或文档的最快方法就是使用全局标头中的“搜索”。 使用痕迹导航链接返回到前几页。
>
观看此视频，了解如何在 Azure 门户中使用全局搜索。


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 门户中使用全局搜索](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>后续步骤

* 在[支持的浏览器和设备](../azure-portal/azure-portal-supported-browsers-devices.md)中详细了解运行 Azure 门户的位置
* 随时随地与 [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)保持连接
* 通过 [Azure 快速入门中心](../azure-portal/azure-portal-quickstart-center.md)载入和设置云环境
