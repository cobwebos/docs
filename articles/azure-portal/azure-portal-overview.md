---
title: Azure 门户概述 | Microsoft Docs
description: Azure 门户是一个图形用户界面，可用于管理 Azure 服务。 了解如何在 Azure 门户中导航和查找资源。
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310724"
---
# <a name="azure-portal-overview"></a>Azure 门户概述

本文介绍 Azure 门户及其页面元素，并帮助你熟悉 Azure 门户的管理体验。

## <a name="what-is-the-azure-portal"></a>什么是 Azure 门户？

Azure 门户是一种基于 Web 的统一控制台，它提供了命令行工具的替代方法。 在 Azure 门户中，可以使用图形用户界面来管理 Azure 订阅。 可以生成、管理和监视从简单 Web 应用到复杂云部署的所有资源。 创建自定义仪表板以有序地显示资源。 配置辅助功能选项以提供最佳体验。

Azure 门户旨在实现复原能力和持续可用性。 每个 Azure 数据中心都提供该门户。 此配置使得 Azure 门户能够灵活应对各种数据中心故障，此外，由于部署位置靠近用户，可以避免网络减速。 Azure 门户不断更新，不需要停机即可进行维护活动。

## <a name="azure-portal-menu"></a>Azure 门户菜单

可为门户菜单选择默认模式。 可以停靠菜单，或者以浮出面板的形式显示菜单。

当门户菜单处于浮出模式时，在调用之前它会隐藏。 选择菜单图标可以打开或关闭菜单。

![处于浮出模式的 Azure 门户菜单](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

如果为门户菜单选择停靠模式，则它始终可见。 可以折叠菜单以提供更多的工作空间。

![处于停靠模式的 Azure 门户菜单](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure 主页

作为 Azure 服务的新订阅者，[登录到门户](https://portal.azure.com)后，看到的第一个画面就是“Azure 主页”。**** 在此页面中，可以编译资源来帮助你充分利用 Azure 订阅。 其中包含了免费在线课程、文档、核心服务和有用站点的链接，使你随时掌握和应对组织发生的变化。 为了方便你快速访问正在处理的工作，该页面还会显示最近访问的资源列表。 无法自定义此页面，但可以选择是要将“Azure 主页”还是“Azure 仪表板”用作默认视图。******** 首次登录时，该页面的顶部会提示你要在哪个位置保存首选项。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-default-view.png)

可以在“门户设置”中更改 Azure 门户菜单和 Azure 默认视图。**** 如果更改所做的选择，会立即应用更改。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure 仪表板

仪表板提供订阅中对你最重要的资源的聚焦视图。 我们已提供一个默认的仪表板来帮助你开始处理资源。 可以自定义此仪表板，以将最常用的资源放到单个视图中。 对默认视图所做的任何更改只会影响你自己的体验。 但是，你可以创建其他仪表板供自己使用，或者发布自定义的仪表板并与组织中的其他用户共享。 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>门户的布局

最好是了解门户的基本布局以及如何与之交互。 本文将会介绍用户界面的组件，以及说明中使用的一些术语。 有关门户的更详细的教程，请参阅课程课程[导航门户](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)。

Azure 门户菜单和页头是始终都会显示的全局元素。 这些持久性功能是用户界面中与每个服务或功能关联的“外壳”，在页头中可以访问全局控件。 资源的配置页面（有时称为“边栏选项卡”）还可能会提供一个资源菜单，以帮助你在功能之间切换。

下图标示了 Azure 门户的基本元素；下表描述了其中的每个元素。

![显示门户全屏视图和 UI 元素键的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![显示扩展门户菜单的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|键|描述
|:---:|---|
|1|页头。 显示在每个门户页面的顶部，包含全局元素。|
|2| 全局搜索。 使用搜索栏可以快速查找特定的资源、服务或文档。|
|3|全局控件。 与所有全局元素一样，这些功能会在整个门户中持续存在，包括：云外壳、订阅筛选器、通知、门户设置、帮助和支持，以及向我们发送反馈。|
|4|你的帐户。 查看有关帐户的信息、切换目录、注销，或使用其他帐户登录。|
|5|门户菜单。 门户菜单是一个全局元素，可帮助你在服务之间导航。 门户菜单有时也称为侧栏，可在“门户设置”中更改其模式。****|
|6|资源菜单。 许多服务都提供一个资源菜单用于帮助管理服务。 此元素有时称为左窗格。|
|7|命令栏。 命令栏上的控件与当前聚焦的元素相关。|
|8|工作窗格。  显示有关当前聚焦的资源的详细信息。|
|9|痕迹导航。 可以使用痕迹导航链接在工作流中后退一个级别。|
|10|用于在当前订阅中创建新资源的主控件。 展开或打开门户菜单，找到“+ 创建资源”。**** 在 Azure 市场中搜索或浏览所要创建的资源类型。|
|11|收藏夹列表。 请参阅[添加、删除和排序收藏项目](../azure-portal/azure-portal-add-remove-sort-favorites.md)，了解如何自定义该列表。|

## <a name="get-started-with-services"></a>开始使用服务

如果你是新订阅者，必须先创建一个资源，这样才有可管理的内容。 选择“+ 创建资源”查看 Azure 市场中提供的服务。**** 在市场中可以看到来自数百家提供商的应用程序和服务，所有这些应用程序和服务都已获准在 Azure 上运行。

我们已在侧栏上的“收藏夹”中预先填充了常用服务的链接。  若要查看所有可用服务，请在侧栏中选择“所有服务”。****

> [!TIP]
> 使用全局页头中的“搜索”可以最快地找到资源、服务或文档。** 使用痕迹导航链接可以回到前面的页面。
>
观看此视频，了解如何在 Azure 门户中使用全局搜索。


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 门户中使用全局搜索](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>后续步骤

* 在[支持的浏览器和设备](../azure-portal/azure-portal-supported-browsers-devices.md)中详细了解可在哪些位置运行 Azure 门户
* 使用 [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)随时随地保持连接
* 使用[Azure 快速启动中心](../azure-portal/azure-portal-quickstart-center.md)进行板载并设置云环境
