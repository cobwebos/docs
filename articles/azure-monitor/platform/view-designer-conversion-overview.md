---
title: Azure Monitor 视图设计器到工作簿的转换指南
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 19fd1d064a3f106d7b770481d1e456c51cf1e166
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759421"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor 视图设计器到工作簿的转换指南
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它让你能够创建自定义视图，帮助你通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 它们被转换到工作簿以提供一块灵活的画布，用于分析数据以及在 Azure 门户中创建丰富的可视报表。 本文可帮助你将视图设计器转换为工作簿。 


## <a name="workbooks-overview"></a>工作簿概述
[工作簿](../insights/vminsights-workbooks.md) 将文本、 [日志查询](../log-query/query-language.md)、指标和参数合并到丰富的交互式报表中。 对 Azure 资源拥有相同访问权限的团队成员也可以编辑工作簿。

在如下所述的场景中，工作簿非常有用：

-   当你事先不知道所需的指标时，浏览虚拟机的使用情况：CPU 利用率、磁盘空间、内存、网络依赖关系，等等。与其他使用情况分析工具不同，工作簿可以结合多个类型的可视化效果和分析，非常适合这种自由探索。
-   通过显示关键计数器的指标和其他日志事件，向团队解释最近预配的 VM 的性能如何。
-   与团队的其他成员分享调整 VM 试验规模的结果。 可以用文本解释试验的目标，然后展示用于评估试验的每个使用情况指标和 Analytics 查询，以及说明每个指标是否高于或低于目标的标注。
-   结合数据、文本说明和后续步骤讨论，报告故障对 VM 使用的影响，从而防止未来发生故障。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>为何要将视图设计器仪表板转换为工作簿？

视图设计器提供了生成基于查询的不同视图和可视化效果的功能。 但是，许多高级自定义仍然受限，如设置网格和磁贴布局的格式，或者选择替换图形来表示数据。 视图设计器限制为总共九个不同的磁贴来表示数据。

工作簿是一个平台，用于释放数据的全部潜能。 工作簿不仅保留所有功能，还通过文本、指标、参数等支持其他功能。 例如，工作簿允许用户合并密集的网格，并添加搜索栏来轻松筛选和分析数据。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>使用工作簿而不使用视图设计器可带来的好处

* 支持日志和指标。
* 允许用于个人访问控制的个人视图和共享工作簿视图。
* 具有选项卡、大小调整功能和缩放控件的自定义布局选项。
* 支持跨多个 Log Analytics 工作区、Application Insights 应用程序和订阅进行查询。
* 允许可动态更新关联的图表和可视化效果的自定义参数。
* 来自公共 GitHub 的模板库支持。

尽管本指南只是提供了用于直接重新创建多个常用视图设计器视图的简单步骤，但工作簿还允许用户自由创建和设计任何自定义的可视化效果和指标。 以下屏幕截图取自[工作区用法模板](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)，显示了工作簿可创建的内容示例：


![工作簿应用示例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>如何开始使用工作簿
可以在边侧导航栏中紧靠在“视图设计器”位置下面，选择“工作簿”项打开工作簿。

![“工作簿”导航项](media/view-designer-conversion-overview/workbooks-nav.png)

选择后，将显示一个库，其中列出了为工作区保存的所有工作簿和模板。

![工作簿库](media/view-designer-conversion-overview/workbooks-gallery.png)

若要启动新工作簿，可以在“快速启动”下选择“空”模板，或选择顶部导航栏中的“新建”图标。   若要查看模板或返回保存的工作簿，请从库中选择该项，或在搜索栏中搜索名称。

若要保存工作簿，需要使用特定的标题、订阅、资源组和位置保存报表。
工作簿将自动填充到与 LA 工作区相同的设置并使用相同的订阅和资源组，但用户可以更改这些报表设置。 默认情况下，工作簿将保存到“我的报表”，只能由单个用户访问。 还可以将其直接保存到共享报表，或者在以后共享。

![工作簿保存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>后续步骤

- [转换选项](view-designer-conversion-options.md)
