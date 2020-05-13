---
title: 在许多工作区中一次性使用 Azure Sentinel 事件 |Microsoft Docs
description: 如何在 Azure Sentinel 中同时查看多个工作区中的事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124157"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>同时处理多个工作区中的事件 

 为了充分利用 Azure Sentinel 的功能，Microsoft 建议使用单工作区环境。 但是，在某些情况下，有些用例需要有多个工作区（在某些情况下，例如，[托管安全服务提供商（MSSP）](./multiple-tenants-service-providers.md)和其客户）可跨多个租户。 利用**多个工作区视图**，你可以在多个工作区中同时查看和处理安全事件，甚至跨租户，使你能够完全查看和控制组织的安全响应能力。

## <a name="entering-multiple-workspace-view"></a>输入多个工作区视图

打开 Azure Sentinel 时，会显示所有所选租户和订阅中具有访问权限的所有工作区的列表。 每个工作区名称的左侧都有一个复选框。 单击单个工作区的名称将进入该工作区。 若要选择多个工作区，请单击所有对应的复选框，然后单击页面顶部的 "**多个工作区视图**" 按钮。

> [!IMPORTANT]
> 多个工作区视图当前支持最多10个并发显示工作区。 
> 
> 如果检查的工作区超过10个，将显示一条警告消息。

请注意，在工作区列表中，可以看到与每个工作区相关联的目录、订阅、位置和资源组。 目录对应于租户。

   ![选择多个工作区](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>使用事件

在**多个工作区视图**中，目前只有**事件**屏幕可用。 它的外观和功能与常规**事件**屏幕类似。 不过，有几个重要的区别：

   ![查看多个工作区中的事件](./media/multiple-workspace-view/incidents.png)

- 处于打开状态的*事件*、*新事件**正在进行*等的顶部的计数器-显示所有所选工作区的总数量。

- 你将在单个统一列表中看到所有选定工作区和目录（租户）的事件。 除了常规**事件**屏幕中的筛选器外，还可以按工作区和目录筛选列表。

- 对于从中选择了事件的所有工作区，你将需要拥有读取和写入权限。 如果你在某些工作区上只具有读取权限，则在这些工作区中选择 "事件" 时，你将看到警告消息。 你将不能修改这些事件，也不能修改与这些事件一起选择的任何其他事件（即使你对其他事件具有权限）。

- 如果选择单个事件，并单击 "**查看完整详细信息**" 或 "**调查**"，则将从该事件的工作区的数据上下文中，而不是其他事件。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何在多个 Azure Sentinel 工作区中同时查看和处理事件。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

