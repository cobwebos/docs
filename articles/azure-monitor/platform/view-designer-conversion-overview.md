---
title: Azure Monitor 查看设计器到工作簿过渡指南
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 234da921b4f0d1243ca8cfdb12ba2d851db2b43f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658687"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor 查看设计器到工作簿过渡指南
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它允许您创建自定义视图来帮助您通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 它们将被分段并替换为提供其他功能的工作簿。 本文概述了将现有视图转换为工作簿的过程。

## <a name="workbooks-overview"></a>工作簿概述
[工作簿](../insights/vminsights-workbooks.md)将文本、 [日志查询](../log-query/query-language.md)、指标和参数合并到丰富的交互式报表中。 具有 Azure 资源访问权限的团队成员还可以编辑工作簿。

工作簿适用于如下方案：

-   如果事先不知道感兴趣的指标，请浏览虚拟机的使用情况： CPU 使用率、磁盘空间、内存、网络依赖项等。与其他使用情况分析工具不同，工作簿允许您组合多种类型的可视化和分析，使其适合这种自由格式浏览。
-   通过显示密钥计数器和其他日志事件的指标，向团队说明最近预配的 VM 的执行方式。
-   与团队的其他成员共享 VM 调整大小试验的结果。 您可以使用文本解释实验的目标，然后显示用于评估实验的每个使用情况指标和 analytics 查询，以及每个指标是否高于或低于目标的明确调用。
-   报告使用 VM 的中断的影响，组合数据、文本说明，并讨论后续步骤以防止将来出现故障。


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>为什么将视图设计器仪表板转换为工作簿？

视图设计器提供生成不同的基于查询的视图和可视化效果的功能。 但是，许多高级自定义仍然受限，如设置网格和磁贴布局的格式，或者选择替换图形来表示数据。 视图设计器限制为总共9个不同的磁贴来表示数据。

工作簿是一个平台，用于释放数据的全部潜能。 工作簿不仅保留所有功能，还支持通过文本、指标、参数等其他功能。 例如，工作簿允许用户合并密集网格，并添加搜索栏来轻松筛选和分析数据。 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>在视图设计器中使用工作簿的优点

* 支持日志和指标。
* 允许个人视图用于单个访问控制和共享工作簿视图。
* 具有选项卡、调整大小和缩放控件的自定义布局选项。
* 支持跨多个 Log Analytics 工作区、Application Insights 应用程序和订阅进行查询。
* 启用动态更新关联的图表和可视化对象的自定义参数。
* 来自公共 GitHub 的模板库支持。

虽然本指南提供了直接重新创建几个常用视图设计器视图的简单步骤，但工作簿允许用户自由地创建和设计其任意自定义可视化效果和指标。 以下屏幕截图来自[工作区使用情况模板](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook)，并显示了能够创建的工作簿示例：


![工作簿应用程序示例](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>如何开始使用工作簿
在 Log Analytics 工作区中打开工作簿时，将在右侧导航栏中作为项直接在视图设计器位置下启用。

![工作簿导航](media/view-designer-conversion-overview/workbooks-nav.png)

选择后，将显示一个库，其中列出了工作区的所有已保存工作簿和模板。

![工作簿库](media/view-designer-conversion-overview/workbooks-gallery.png)

若要启动新的工作簿，可以在 "**快速启动**" 下选择**空**模板，或在顶部导航栏中选择 "**新建**" 图标。 若要查看模板或返回保存的工作簿，请从库中选择该项，或在搜索栏中搜索名称。

若要保存工作簿，需要使用特定的标题、订阅、资源组和位置保存报表。
工作簿将自动填充到与 LA 工作区相同的设置，具有相同的订阅、资源组，但用户可以更改这些报表设置。 默认情况下，工作簿保存到*我的报表*，只能由单个用户访问。 它们还可以直接保存到共享报表中或在以后共享。

![工作簿保存](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>后续步骤

- [转换选项](view-designer-conversion-options.md)
