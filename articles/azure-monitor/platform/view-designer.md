---
title: 创建视图以分析 Azure Monitor 中的日志数据 |Microsoft Docs
description: 通过使用 Azure Monitor 中的视图设计器，你可以创建在 Azure 门户中显示的自定义视图，并在 Log Analytics 工作区中包含有关数据的多种可视化效果。 本文包含视图设计器的概述，并提供了创建和编辑自定义视图的过程。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 05d849aa0edf5892e568c4078ca619490cfcd98e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363194"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>使用 Azure Monitor 中的视图设计器创建自定义视图
通过使用 Azure Monitor 中的视图设计器，你可以在 Azure 门户中创建各种自定义视图，这些视图可帮助你可视化 Log Analytics 工作区中的数据。 本文概述了视图设计器以及创建和编辑自定义视图的过程。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

有关视图设计器的详细信息，请参阅：

* [磁贴参考](view-designer-tiles.md)：针对自定义视图中的每个可用磁贴，提供了设置方面的参考指南。
* [可视化部件参考](view-designer-parts.md)：针对自定义视图中可用的可视化部件，提供了设置方面的参考指南。


## <a name="concepts"></a>概念
视图显示在 Azure 门户的 Azure Monitor "**概述**" 页中。 通过点击“见解”部分下的“更多”，从“Azure Monitor”菜单打开此页面。 每个自定义视图中的磁贴会按字母顺序显示，并且监视解决方案的磁贴将安装在同一工作区中。

![概述页](media/view-designer/overview-page.png)

使用视图设计器创建的视图所包含的元素在下表中进行了说明：

| 组成部分 | Description |
|:--- |:--- |
| 磁贴 | 显示在 Azure Monitor "**概述**" 页上。 每个磁贴都会显示一个可视化摘要，其中包含磁贴所代表的自定义视图。 每个磁贴类型提供的记录可视化效果各不相同。 选择磁贴即可显示自定义视图。 |
| 自定义视图 | 在选择磁贴时显示。 每个视图包含一个或多个可视化部件。 |
| 可视化部件 | 基于一个或多个[日志查询](../log-query/log-query-overview.md)在 Log Analytics 工作区中显示数据的可视化。 大多数部件会包括提供高级可视化效果的标头，以及显示最匹配结果的列表。 每个部件类型提供了 Log Analytics 工作区中记录的不同可视化效果。 您可以选择部分中的元素来执行提供详细记录的日志查询。 |

## <a name="required-permissions"></a>所需的权限
你需要在 "Log Analytics" 工作区中至少具有 "[参与者" 级别权限](manage-access.md#manage-access-using-azure-permissions)才能创建或修改视图。 如果你没有此权限，则 "视图设计器" 选项将不会显示在菜单中。


## <a name="work-with-an-existing-view"></a>使用现有视图
使用视图设计器创建的视图显示以下选项：

![概述菜单](media/view-designer/overview-menu.png)

下表描述了这些选项：

| 选项 | Description |
|:--|:--|
| 刷新   | 使用最新数据刷新视图。 | 
| 日志      | 打开[Log Analytics](../log-query/portals.md)用日志查询分析数据。 |
| 编辑       | 在视图设计器中打开视图，以便编辑其内容和配置。  |
| 克隆      | 创建一个新视图，并在视图设计器中打开它。 新视图的名称与原始名称相同，但其末尾附加了 *Copy* 字样。 |
| 日期范围 | 为视图中包含的数据设置日期和时间范围筛选器。 在视图中的查询中设置任何日期范围前，将应用此日期范围。  |
| +          | 定义为视图定义的自定义筛选器。 |


## <a name="create-a-new-view"></a>创建新视图
可以在视图设计器中创建新视图，方法是在 Log Analytics 工作区的菜单中选择“视图设计器”。

![视图设计器磁贴](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>使用视图设计器
使用视图设计器来创建新视图或编辑现有视图。 

视图设计器有三个窗格： 
* **设计**：包含正在创建或编辑的自定义视图。 
* **控件**：包含添加到“设计”窗格的磁贴和部件。 
* **属性**：显示磁贴或所选部件的属性。

![视图设计器](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>配置视图磁贴
一个自定义视图只能有一个磁贴。 若要查看当前磁贴或选择另一个磁贴，请在“控件”窗格中选择“磁贴”选项卡。 “属性”窗格会显示当前磁贴的属性。 

可以根据[磁贴参考](view-designer-tiles.md)中的信息配置磁贴属性，然后单击“应用”，保存所做的更改。

### <a name="configure-the-visualization-parts"></a>配置可视化部件
视图中可以包含任何数量的可视化部件。 若要向视图添加部件，请选择“视图”选项卡，然后选择可视化部件。 “属性”窗格显示所选部件的属性。 

可以根据[可视化部件参考](view-designer-parts.md)中的信息配置视图属性，然后单击“应用”，保存所做的更改。

视图只有一行可视化部件。 可以重新排列现有部件，只需将其拖至新位置即可。

可以通过以下方式从视图中删除可视化部件：选择该部件右上角的“X”。


### <a name="menu-options"></a>菜单选项
下表介绍了以编辑模式使用视图的选项。

![编辑菜单](media/view-designer/edit-menu.png)

| 选项 | Description |
|:--|:--|
| 保存        | 保存所做的更改并关闭视图。 |
| 取消      | 放弃所做的更改并关闭视图。 |
| 删除视图 | 删除视图。 |
| 导出      | 将视图导出至 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)，可将此模板导入其它工作区。 文件的名称为视图的名称，包含 *omsview* 扩展名。 |
| Import      | 导入从另一个工作区中导出的 *omsview* 文件。 此操作覆盖现有视图的配置。 |
| 克隆       | 创建一个新视图，并在视图设计器中打开它。 新视图的名称与原始名称相同，但其末尾附加了 *Copy* 字样。 |

## <a name="next-steps"></a>后续步骤
* 将[磁贴](view-designer-tiles.md)添加到自定义视图。
* 将[可视化部件](view-designer-parts.md)添加到自定义视图。
