---
title: Azure 门户概述 | Microsoft Docs
description: 了解如何在 Azure 门户中导航以及使用它来管理服务
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 05/24/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: de04b461d8e2d2dce9a88052a86fbe5d163fafec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244183"
---
# <a name="azure-portal-overview"></a>Azure 门户概述

本文介绍 Azure 门户及其页面元素，并帮助你熟悉 Azure 门户的管理体验。

## <a name="what-is-the-azure-portal"></a>什么是 Azure 门户？

Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。 在 Azure 门户中，可以使用图形用户界面来管理 Azure 订阅。 可以生成、管理和监视从简单 Web 应用到复杂云部署在内的各种资源，创建自定义的仪表板来显示组织有序的资源视图，以及配置辅助功能选项以获得最佳体验。

在 Azure 门户设计用于复原和连续可用性。 它，从而使其可复原到单个数据中心故障的每个 Azure 数据中心中已存在并通过靠近用户正在网络突降还避免了。 在 Azure 门户持续更新和维护活动要求没有停机时间。

## <a name="azure-home"></a>Azure 主页

作为 Azure 服务的新订阅者，[登录到门户](https://portal.azure.com)后，看到的第一个画面就是“Azure 主页”。  在此页面中，可以编译资源来帮助你充分利用 Azure 订阅。 其中包含了免费在线课程、文档、核心服务和有用站点的链接，使你随时掌握和应对组织发生的变化。 为了方便你快速访问正在处理的工作，该页面还会显示最近访问的资源列表。 无法自定义此页面，但可以选择是要将“Azure 主页”还是“Azure 仪表板”用作默认视图。   首次登录时，该页面的顶部会提示你要在哪个位置保存首选项。

![显示默认视图选择器的屏幕截图](./media/azure-portal-overview/azure-portal-default-view.png)

## <a name="azure-dashboard"></a>Azure 仪表板

仪表板提供订阅中对你最重要的资源的聚焦视图。 我们已提供一个默认的仪表板来帮助你开始处理资源。 可以自定义此仪表板，以将最常用的资源放到单个视图中。 对默认视图所做的任何更改只会影响你自己的体验。 但是，你可以创建其他仪表板供自己使用，或者发布自定义的仪表板并与组织中的其他用户共享。 有关详细信息，请参阅[在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="getting-around-the-portal"></a>门户的布局

最好是了解门户的基本布局以及如何与之交互。 本文将会介绍用户界面的组件，以及说明中使用的一些术语。 在门户的更详细教程，请参阅课程课[在门户中导航](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)。

Azure 门户侧栏和页头是始终都会显示的全局元素。 这些持久性功能是用户界面中与每个服务或功能关联的“外壳”，在页头中可以访问全局控件。 资源的配置页面（有时称为“边栏选项卡”）还可能会提供一个左窗格，以帮助你在功能之间切换。

下图标示了 Azure 门户的基本元素；下表描述了其中的每个元素。

![显示门户全屏视图和 UI 元素键的屏幕截图](./media/azure-portal-overview/azure-portal-fullscreen-map.png)

|密钥|描述
|:---:|---|
|第|页头。 显示在每个门户页面的顶部，包含全局元素。|
|2| 全局搜索。 使用搜索栏可以快速查找特定的资源、服务或文档。|
|3|全局控件。 与所有全局元素一样，这些功能在门户的各个页面中都会出现，其中包括：Cloud Shell、订阅筛选器、通知、门户设置、帮助和支持，以及向我们发送反馈。|
|4|你的帐户。 查看有关帐户的信息、切换目录、注销，或使用其他帐户登录。|
|5|侧栏。 侧栏是一个全局元素，可帮助你在服务之间导航。 可以折叠侧栏，以便将注意力集中在工作窗格上。|
|6|用于在当前订阅中创建新资源的主控件。 在 Azure 市场中搜索或浏览所要创建的资源类型。|
|7|收藏夹列表。 在“所有服务”页面中添加或删除收藏项目。 |
|8|左窗格。 许多服务都提供左窗格菜单用于帮助管理服务。|
|9|命令栏。 命令栏上的控件与当前聚焦的元素相关。|
|10|痕迹导航。 可以使用痕迹导航链接在工作流中后退一个级别。|
|11|工作窗格。  显示有关当前聚焦的资源的详细信息。|

## <a name="get-started-with-services"></a>开始使用服务

如果你是新订阅者，必须先创建一个资源，这样才有可管理的内容。 选择“+创建资源”查看 Azure 市场中提供的服务。  在市场中可以看到来自数百家提供商的应用程序和服务，所有这些应用程序和服务都已获准在 Azure 上运行。

我们已在侧栏上的“收藏夹”中预先填充了常用服务的链接。  若要查看所有可用服务，请在侧栏中选择“所有服务”。 

> [!TIP]
> 使用全局页头中的“搜索”可以最快地找到资源、服务或文档。  使用痕迹导航链接可以回到前面的页面。
>

## <a name="next-steps"></a>后续步骤

* 在[支持的浏览器和设备](../azure-portal/azure-portal-supported-browsers-devices.md)中详细了解可在哪些位置运行 Azure 门户

* 使用 [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)随时随地保持连接
