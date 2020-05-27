---
title: 同时处理许多工作区中的 Azure Sentinel 事件 | Microsoft Docs
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
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124157"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>同时处理许多工作区中的事件 

 为了充分利用 Azure Sentinel 的功能，Microsoft 建议使用单工作区环境。 但是，在某些情况下，有些用例需要有多个跨多个租户的工作区，例如[托管安全服务提供商 (MSSP)](./multiple-tenants-service-providers.md) 及其客户就是如此。 利用**多工作区视图**，你可以同时跨多个工作区（甚至跨租户）查看和处理安全事件，因此能够完全洞察和控制组织的安全响应能力。

## <a name="entering-multiple-workspace-view"></a>进入多工作区视图

打开 Azure Sentinel 时，会显示一个列表，其中列出了所有选定租户和订阅中你具有访问权限的所有工作区。 每个工作区名称的左侧都有一个复选框。 单击单个工作区的名称将进入该工作区。 若要选择多个工作区，请单击所有对应的复选框，然后单击页面顶部的“多工作区视图”按钮。

> [!IMPORTANT]
> 多工作区视图当前支持最多同时显示 10 个工作区。 
> 
> 如果选中的工作区超过 10 个，则会显示一条警告消息。

请注意，在工作区列表中，可以看到与每个工作区关联的目录、订阅、位置和资源组。 目录对应于租户。

   ![选择多个工作区](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>处理事件

在“多工作区视图”中，目前仅提供了“事件”屏幕。 它的外观和功能非常类似于常规“事件”屏幕。 但二者之间存在一些重要的差异：

   ![查看多个工作区中的事件](./media/multiple-workspace-view/incidents.png)

- 页面顶部的计数器（“待处理事件”、“新事件”、“正在进行”，等等）显示与所有选定工作区对应的总数。

- 你将在单个统一列表中看到所有选定工作区和目录（租户）的事件数。 除了常规“事件”屏幕中的筛选器外，你还可以按工作区和目录筛选列表。

- 你需要对你从中选择了事件的所有工作区具有读写权限。 如果你在某些工作区上只具有读取权限，则选择这些工作区中的事件时，将会显示警告消息。 你将不能修改这些事件，也不能修改与这些事件一起选择的任何其他事件（即使你对其他事件具有权限）。

- 如果选择单个事件并单击“查看完整详细信息”或“调查”，则从那时起你将处于该事件的工作区的数据上下文中，不再处于其他事件的上下文中。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何同时查看和处理多个 Azure Sentinel 工作区中的事件。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

