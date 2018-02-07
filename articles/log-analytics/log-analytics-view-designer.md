---
title: "创建视图来分析 Azure Log Analytics 中的数据 | Microsoft Docs"
description: "通过 Log Analytics 中的视图设计器可创建自定义视图，其可在 Azure 门户中显示，且包含 Log Analytics 工作区中数据的多种可视化效果。 本文包含了视图设计器以及创建和编辑自定义视图的过程概述。"
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
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>使用视图设计器在 Log Analytics 中创建自定义视图
通过 [Log Analytics](log-analytics-overview.md) 中的视图设计器可在 Azure 门户中创建自定义视图，其包含 Log Analytics 工作区中不同数据的可视化效果。 本文包含了视图设计器以及创建和编辑自定义视图的过程概述。

有关视图设计器的其他文章包括：

* [磁贴参考](log-analytics-view-designer-tiles.md) - 可在自定义视图中使用的每个磁贴的设置参考。
* [可视化部分参考](log-analytics-view-designer-parts.md) - 针对可在自定义视图中使用的每个磁贴的设置参考。

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则必须使用[新查询语言](https://go.microsoft.com/fwlink/?linkid=856078)编写所有视图中的查询。  将自动转换工作区升级前创建的任何视图。

## <a name="concepts"></a>概念
视图显示在 Azure 门户中 Log Analytics 工作区内的“概述”页中。  每个自定义视图的磁贴都按字母顺序显示，同时显示的还有安装在同一工作区中的解决方案的磁贴。

![概述页](media/log-analytics-view-designer/overview-page.png)

使用视图设计器创建的视图中含有下表中的元素。

| 部分 | 说明 |
|:--- |:--- |
| 磁贴 |显示在 Log Analytics 工作区的概述页上。  包括自定义视图中所包含的信息的视觉汇总。  不同的磁贴类型提供了记录的不同可视化效果。  单击该磁贴以打开自定义视图。 |
| 自定义视图 |当用户单击该磁贴时显示。  包含一个或多个可视化部件。 |
| 可视化部件 |Log Analytics 工作区中的数据可视化效果基于一个或多个[日志搜索](log-analytics-log-searches.md)。  大多数部件会包括提供高级别可视化效果的标头和最匹配的结果的列表。  不同的部件类型提供了 Log Analytics 工作区中记录的不同可视化效果。  单击部件中的元素，以执行会提供详细记录的日志搜索。 |


## <a name="work-with-an-existing-view"></a>使用现有视图
在打开使用视图设计器创建的视图时，将显示包含下表所示选项的菜单。

![概述菜单](media/log-analytics-view-designer/overview-menu.png)


| 选项 | 说明 |
|:--|:--|
| 刷新   | 使用最新数据刷新视图。 | 
| 分析 | 打开[高级分析门户](log-analytics-log-search-portals.md#advanced-analytics-portal)，使用日志搜索分析数据。(log-analytics-log-search-portals.md#advanced-analytics-portal)。 |
| 筛选器    | 为包含在视图中的数据设置时间筛选器。 |
| 编辑      | 在视图设计器中打开视图，以便编辑内容和配置。   |
| 克隆     | 创建一个新视图，并在视图设计器中打开它。  新视图将具有与原始视图相同的名称，其名称末尾会附加“Copy”。 |


## <a name="create-a-new-view"></a>创建新视图
在“视图设计器”中创建新视图，方法是在 Azure 门户的 Log Analytics 工作区中的概述页上单击“视图设计器”磁贴。

![视图设计器磁贴](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>使用视图设计器
无论是新建视图还是编辑现有视图，都将用到“视图设计器”。  

视图设计器有三个窗格。  “设计”窗格中包含正在创建或编辑的自定义视图。  将磁贴和部件从“控件”窗格添加到“设计”窗格。  “属性”窗格会显示该磁贴或所选部件的属性。

![视图设计器](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>配置视图磁贴
一个自定义视图只能有一个磁贴。  在“控件”窗格中选择“磁贴”选项卡，以查看当前磁贴或选择另一个磁贴。  “属性”窗格会显示当前磁贴的属性。  根据 [磁贴引用](log-analytics-view-designer-tiles.md) 中的详细信息配置磁贴属性，并单击 **应用** 保存更改。

### <a name="configure-visualization-parts"></a>配置可视化部件
视图中可以包含任何数量的可视化部件。  选择“视图”选项卡，并选择要添加到该视图中的可视化部件。  “属性”窗格会显示所选部件的属性。  根据 [可视化部件引用](log-analytics-view-designer-parts.md) 中的详细信息配置视图属性，并单击“应用”保存更改。

视图只有一行可视化部件。  通过以下方式重新排列视图中的现有部件：单击这些部件并将其拖动到新位置。

可以通过以下方式从视图中删除可视化部件：单击该部件右上角的“X”按钮。


### <a name="menu-options"></a>菜单选项
在编辑模式下处理视图时，可使用下表所列出的各菜单选项。

![编辑菜单](media/log-analytics-view-designer/edit-menu.png)

| 选项 | 说明 |
|:--|:--|
| 保存        | 保存更改并关闭视图。 |
| 取消      | 放弃更改并关闭视图。 |
| 删除视图 | 删除视图。 |
| 导出      | 将视图导出至[资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)，可将此模板导入其它工作区。  该文件的名称将为该视图的名称，扩展名为 *omsview*。 |
| 导入      | 导入从另一个工作区中导出的 omsview 文件。  这将覆盖现有视图的配置。 |
| 克隆       | 创建一个新视图，并在视图设计器中打开它。  新视图将具有与原始视图相同的名称，其名称末尾会附加“Copy”。 |
| 发布     | 将视图导出到可插入[管理解决方案](../operations-management-suite/operations-management-suite-solutions-resources-views.md)的 JSON 文件。  该文件的名称将为该视图的名称，扩展名为 json。 使用扩展名 resjson 创建第二个文件，该文件包含在 JSON 文件中定义的资源的值。

## <a name="next-steps"></a>后续步骤
* 将[磁贴](log-analytics-view-designer-tiles.md)添加到自定义视图。
* 将 [可视化部件](log-analytics-view-designer-parts.md) 添加到自定义视图。
