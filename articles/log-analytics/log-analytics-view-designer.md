---
title: "Log Analytics 视图设计器 | Microsoft Docs"
description: "Log Analytics 中的视图设计器允许你在 OMS 控制台中创建自定义视图，以包含 OMS 存储库中数据的多种可视化效果。 本文包含了视图设计器以及创建和编辑自定义视图的过程概述。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4ba6ce876df3ec426e02e10eda062b67d0d42c4e


---
# <a name="log-analytics-view-designer"></a>Log Analytics 视图设计器
Log Analytics 中的视图设计器允许你在 OMS 控制台中创建自定义视图，以包含 OMS 存储库中不同数据的可视化效果。 本文包含了视图设计器以及创建和编辑自定义视图的过程概述。

可用于视图设计器的其他文章有：

* [磁贴参考](log-analytics-view-designer-tiles.md) - 可在自定义视图中使用的每个磁贴的设置参考。 
* [可视化部分参考](log-analytics-view-designer-parts.md) - 针对可在自定义视图中使用的每个磁贴的设置参考。 

## <a name="concepts"></a>概念
使用视图设计器创建的视图中含有下表中的元素。

| 部分 | 说明 |
|:--- |:--- |
| 磁贴 |显示在 Log Analytics 的主概述仪表板中。  包括自定义视图中所包含的信息的视觉汇总。  不同的磁贴类型提供了 OMS 存储库中记录的不同可视化效果。  单击该磁贴以打开自定义视图。 |
| 自定义视图 |当用户单击该磁贴时显示。  包含一个或多个可视化部件。 |
| 可视化部件 |OMS 存储库中的数据基于一个或多个[日志搜索](log-analytics-log-searches.md)的可视化效果。  大多数部件会包括提供高级别可视化效果的标头和最匹配的结果的列表。  不同的部件类型提供了 OMS 存储库中记录的不同可视化效果。  单击部件中的元素，以执行会提供详细记录的日志搜索。 |

![视图设计器概述](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>将视图设计器添加到工作区
在预览视图设计器时，必须通过以下方式将它添加到工作区：选择 OMS 门户的“设置”部分中的“预览功能”。

![启用预览](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>创建和编辑视图
### <a name="create-a-new-view"></a>创建新视图
通过以下方式在**视图设计器**中打开新视图：在 OMS 主仪表板中单击视图设计器磁贴。

![视图设计器磁贴](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>编辑现有视图
若要在视图设计器中编辑现有视图，请通过以下方式打开该视图：在 OMS 主仪表板中单击其磁贴。  然后单击“编辑”按钮即可在视图设计器中打开该视图。

![编辑视图](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>克隆现有视图
克隆视图时，会创建一个新的视图，然后在视图设计器中打开它。  新视图将具有与原始视图相同的名称（其名称末尾会附加“Copy”）。  若要克隆视图，请通过以下方式打开现有视图：在 OMS 主仪表板中单击其磁贴。  然后单击“克隆”按钮即可在视图设计器中打开该视图。

![克隆视图](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>删除现有视图
若要删除现有视图，请通过以下方式打开该视图：在 OMS 主仪表板中单击其磁贴。  接着单击“编辑”按钮以在视图设计器中打开该视图，然后单击“删除视图”。

![删除视图](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>导出现有视图
可以将视图导出为 JSON 文件，该文件可以导入到另一个工作区，也可以在 [Azure Resource Manager 模板](../resource-group-authoring-templates.md)中使用。  若要导出现有视图，请通过以下方式打开该视图：在 OMS 主仪表板中单击其磁贴。  然后单击“导出”按钮即可在浏览器的下载文件夹中创建一个文件。  该文件的名称将为该视图的名称，扩展名为 *omsview*。

![导出视图](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>导入现有视图
可以导入从另一个管理组中导出的 *omsview* 文件。  若要导入现有视图，请先创建一个新视图。  然后单击“导入”按钮，并选择 *omsview* 文件。  会将该文件中的配置复制到现有视图。

![导出视图](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>使用视图设计器
视图设计器有三个窗格。  “设计”窗格会呈现自定义视图。  当将磁贴和部件从“控件”窗格添加到“设计”窗格时，会将它们添加到视图中。  “属性”窗格会显示该磁贴或所选部件的属性。

![视图设计器](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>配置视图磁贴
一个自定义视图只能有一个磁贴。  在“控件”窗格中选择“磁贴”选项卡，以查看当前磁贴或选择另一个磁贴。  “属性”窗格会显示当前磁贴的属性。  根据“磁贴引用”[](log-analytics-view-designer-tiles.md)中的详细信息配置磁贴属性，然后单击“应用”保存更改。

### <a name="configure-visualization-parts"></a>配置可视化部件
视图中可以包含任何数量的可视化部件。  选择“视图”选项卡，然后选择要添加到该视图中的可视化部件。  “属性”窗格会显示所选部件的属性。  根据“可视化部件引用”[](log-analytics-view-designer-parts.md)中的详细信息配置视图属性，然后单击“应用”保存更改。

### <a name="delete-a-visualization-part"></a>删除可视化部件
可以通过以下方式从视图中删除可视化部件：单击该部件右上角的“X”按钮。

### <a name="rearrange-visualization-parts"></a>重新排列可视化部件
视图只有一行可视化部件。  通过以下方式重新排列视图中的现有部件：单击这些部件并将其拖动到新位置。

## <a name="next-steps"></a>后续步骤
* 将“磁贴”[](log-analytics-view-designer-tiles.md)添加到自定义视图。
* 将“可视化部件”[](log-analytics-view-designer-parts.md)添加到自定义视图。




<!--HONumber=Nov16_HO3-->


