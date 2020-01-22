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
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310724"
---
# <a name="azure-portal-overview"></a>Azure 门户概述

本文介绍 Azure 门户、标识门户页面元素，并帮助你熟悉 Azure 门户管理体验。

## <a name="what-is-the-azure-portal"></a>什么是 Azure 门户？

Azure 门户是一种基于 Web 的统一控制台，它提供了命令行工具的替代方法。 使用 Azure 门户，可以使用图形用户界面来管理 Azure 订阅。 你可以从简单的 web 应用程序到复杂的云部署，生成、管理和监视一切内容。 为资源的组织视图创建自定义仪表板。 配置辅助功能选项以获得最佳体验。

Azure 门户旨在实现复原和持续可用性。 它存在于每个 Azure 数据中心。 此配置使 Azure 门户可以灵活应对单个数据中心故障，并通过接近用户来避免网络速度降低。 Azure 门户会持续更新，并且维护活动无需停机。

## <a name="azure-portal-menu"></a>Azure 门户菜单

可以为门户菜单选择默认模式。 它可以停靠，也可以作为飞出的面板。

当门户菜单处于飞入模式时，它会一直处于隐藏状态，直到您需要它。 选择菜单图标以打开或关闭菜单。

![在飞入模式下 Azure 门户菜单](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

如果为门户菜单选择停靠模式，则它将始终可见。 可以折叠菜单以提供更多的工作空间。

![停靠模式下 Azure 门户菜单](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure 主页

作为 Azure 服务的新订阅者，[登录到门户](https://portal.azure.com)后的第一件事就是**azure Home**。 此页编译可帮助你充分利用 Azure 订阅的资源。 我们提供了免费在线课程、文档、核心服务和有用网站的链接，可帮助你保持当前和管理组织的更改。 为了快速轻松地访问正在进行的工作，我们还显示了最近访问过的资源的列表。 你无法自定义此页，但你可以选择是否将**Azure Home**或**azure 仪表板**显示为默认视图。 首次登录时，会在页面顶部出现一个提示，可在其中保存首选项。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-default-view.png)

"Azure 门户" 菜单和 "Azure 默认" 视图都可以在 "**门户" 设置**中进行更改。 如果更改所选内容，则会立即应用更改。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure 仪表板

仪表板提供了对订阅中最重要的资源的集中视图。 我们为你提供了一个用于入门的默认仪表板。 您可以自定义此仪表板，以便将经常使用的资源引入单个视图中。 对默认视图所做的任何更改都会影响你的体验。 但是，你可以为自己使用而创建其他仪表板，也可以发布自定义的仪表板并将其与组织中的其他用户共享。 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>围绕门户

了解基本门户布局以及如何与其进行交互会很有帮助。 在这里，我们将介绍用户界面的组件以及我们用来提供说明的一些术语。 有关门户的更详细教程，请参阅课程课程[导航门户](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)。

Azure 门户菜单和页眉是始终存在的全局元素。 这些持久功能是与每个单独的服务或功能关联的用户界面的 "shell"，标题提供对全局控件的访问。 资源的配置页面（有时也称为 "边栏选项卡"）可能还有一个资源菜单，可帮助您在各功能之间移动。

下图标记了 Azure 门户的基本元素，下表中描述了每个元素。

![显示全屏门户视图和 UI 元素的键的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![显示扩展的门户菜单的屏幕截图](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|密钥|Description
|:---:|---|
|第|页眉。 显示在每个门户页面的顶部并保存全局元素。|
|2| 全局搜索。 使用搜索栏快速查找特定的资源、服务或文档。|
|3|全局控件。 与所有全局元素一样，这些功能在门户中保持不变，包括： Cloud Shell、订阅筛选器、通知、门户设置、帮助和支持，并向我们发送反馈。|
|4|你的帐户。 查看有关你的帐户、切换目录、注销或使用其他帐户登录的信息。|
|5|门户菜单。 门户菜单是一个全局元素，可帮助您在服务之间导航。 有时也称为边栏，门户菜单模式可以在**门户设置**中进行更改。|
|6|资源菜单。 许多服务都包含一个资源菜单，可帮助您管理该服务。 你可能会看到此元素称为左窗格。|
|7|命令栏。 命令栏上的控件与当前焦点相关。|
|8|工作窗格。  显示有关当前处于焦点的资源的详细信息。|
|9|导航. 您可以使用痕迹链接在工作流中上移。|
|10|用于在当前订阅中创建新资源的主控件。 展开或打开门户菜单，查找 " **+ 创建资源**"。 搜索或浏览 Azure Marketplace，了解要创建的资源类型。|
|11|收藏夹列表。 若要了解如何自定义列表，请参阅[添加、删除和排序收藏夹](../azure-portal/azure-portal-add-remove-sort-favorites.md)。|

## <a name="get-started-with-services"></a>服务入门

如果你是新的订阅者，则必须先创建一个资源，然后才能管理任何内容。 选择 " **+ 创建资源**" 以查看 Azure Marketplace 中提供的服务。 你将在此处找到数百个提供商提供的应用程序和服务，所有认证都在 Azure 上运行。

我们在边栏中预先填充了你的收藏夹，其中包含指向常用服务的链接。  若要查看所有可用服务，请从边栏中选择 "**所有服务**"。

> [!TIP]
> 查找资源、服务或文档的最快捷方法是在全局标头中使用 "*搜索*"。 使用痕迹链接返回到上一页。
>
观看此视频，了解如何使用 Azure 门户中的全局搜索。


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[如何在 Azure 门户中使用全局搜索](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>后续步骤

* 详细了解在[支持的浏览器和设备](../azure-portal/azure-portal-supported-browsers-devices.md)中运行 Azure 门户的位置
* 随时保持连接状态[Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* 通过[Azure 快速入门中心](../azure-portal/azure-portal-quickstart-center.md)加入并设置云环境
