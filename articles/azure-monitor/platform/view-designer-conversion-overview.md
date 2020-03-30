---
title: Azure 监视器视图设计器到工作簿过渡指南
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658687"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure 监视器视图设计器到工作簿过渡指南
[视图设计器](view-designer.md)是 Azure 监视器的一项功能，允许您创建自定义视图，以帮助您在日志分析工作区中可视化数据，包括图表、列表和时间线。 它们正在逐步淘汰，代之以提供额外功能的工作簿。 本文概述了将现有视图转换为工作簿的过程。

## <a name="workbooks-overview"></a>工作簿概述
[工作簿](../insights/vminsights-workbooks.md)将文本、 [日志查询](../log-query/query-language.md)、指标和参数合并为丰富的交互式报表。 对 Azure 资源具有相同访问权限的团队成员也可以编辑工作簿。

在如下所述的场景中，工作簿非常有用：

-   当您事先不知道感兴趣的指标时，探索虚拟机的使用：CPU 利用率、磁盘空间、内存、网络依赖关系等。与其他使用情况分析工具不同，工作簿允许您组合多种可视化和分析，使其成为这种自由形式的探索的绝佳去。
-   通过显示关键计数器的指标和其他日志事件，向团队解释最近预配的 VM 的性能如何。
-   与团队的其他成员分享调整 VM 试验规模的结果。 您可以使用文本解释实验的目标，然后显示用于评估实验的每个使用指标和分析查询，以及每个指标是否高于或低于目标的明确呼出。
-   结合数据、文本说明和后续步骤讨论，报告故障对 VM 使用的影响，从而防止未来发生故障。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>为什么要将视图设计器仪表板转换为工作簿？

视图设计器提供了生成不同基于查询的视图和可视化效果的能力。 但是，许多高级自定义仍然受限，如设置网格和磁贴布局的格式，或者选择替换图形来表示数据。 视图设计器仅限于总共九个不同的切片来表示数据。

工作簿是一个平台，用于释放数据的全部潜能。 工作簿不仅保留了所有功能，而且还通过文本、指标、参数等支持其他功能。 例如，工作簿允许用户合并密集网格并添加搜索栏以轻松筛选和分析数据。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>使用工作簿优于视图设计器的优势

* 支持日志和指标。
* 允许个人视图用于个人访问控制和共享工作簿视图。
* 具有选项卡、大小调整和缩放控件的自定义布局选项。
* 支持跨多个日志分析工作区、应用程序见解应用程序和订阅进行查询。
* 启用动态更新关联图表和可视化效果的自定义参数。
* 来自公共 GitHub 的模板库支持。

虽然本指南提供了直接重新创建几个常用视图设计器视图的简单步骤，但工作簿允许用户自由创建和设计任何自己的自定义可视化效果和指标。 以下屏幕截图来自[工作区使用模板](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)，显示了工作簿能够创建的示例：


![工作簿应用程序示例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>如何开始使用工作簿
在日志分析工作区中，在视图设计器位置正下方的侧导航栏中，将启用工作簿中的打开工作簿作为项目。

![工作簿导航](media/view-designer-conversion-overview/workbooks-nav.png)

选中后，将显示一个库，列出工作区保存的所有工作簿和模板。

![工作簿库](media/view-designer-conversion-overview/workbooks-gallery.png)

要启动新的工作簿，您可以在 **"快速启动"** 下选择 **"空**"模板，或选择顶部导航栏中的 **"新建**"图标。 要查看模板或返回到保存的工作簿，请从库中选择项目或在搜索栏中搜索名称。

要保存工作簿，您需要使用特定标题、订阅、资源组和位置保存报表。
工作簿将自动填充到与 LA 工作区相同的设置，使用相同的订阅资源组，但是，用户可以更改这些报表设置。 工作簿默认保存到 *"我的报告"，* 仅由单个用户访问。 它们也可以直接保存到共享报表或稍后共享。

![工作簿保存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>后续步骤

- [转换选项](view-designer-conversion-options.md)
