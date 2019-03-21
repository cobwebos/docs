---
title: 创建视图来分析 Azure Monitor 中的日志数据 |Microsoft Docs
description: 通过 Azure Monitor 中使用视图设计器，可以创建自定义视图，在 Azure 门户中显示并包含各种上 Log Analytics 工作区中数据的可视化效果。 本文包含视图设计器的概述，并提供了创建和编辑自定义视图的过程。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: f07fc2f03ad72e7ee0fd408782b8fe845c88e780
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286561"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>在 Azure Monitor 中使用视图设计器创建自定义视图
通过 Azure Monitor 中使用视图设计器，可以在可帮助你可视化 Log Analytics 工作区中的数据在 Azure 门户中创建各种自定义视图。 本文概述了视图设计器以及创建和编辑自定义视图的过程。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

有关视图设计器的详细信息，请参阅：

* [磁贴参考](view-designer-tiles.md)：针对自定义视图中的每个可用磁贴，提供了设置方面的参考指南。
* [可视化部件参考](view-designer-parts.md)：针对自定义视图中可用的可视化部件，提供了设置方面的参考指南。


## <a name="concepts"></a>概念
视图显示在 Azure Monitor**概述**在 Azure 门户中的页。 通过点击“见解”部分下的“更多”，从“Azure Monitor”菜单打开此页面。 每个自定义视图中的磁贴会按字母顺序、 显示和监视解决方案的磁贴安装在同一工作区。

![概述页](media/view-designer/overview-page.png)

使用视图设计器创建的视图所包含的元素在下表中进行了说明：

| 部分 | 描述 |
|:--- |:--- |
| 磁贴 | 显示在 Azure Monitor**概述**页。 每个磁贴都会显示一个可视化摘要，其中包含磁贴所代表的自定义视图。 每个磁贴类型提供的记录可视化效果各不相同。 选择磁贴即可显示自定义视图。 |
| 自定义视图 | 在选择磁贴时显示。 每个视图包含一个或多个可视化部件。 |
| 可视化部件 | 显示基于一个或多个 Log Analytics 工作区中数据的可视化效果[记录查询](../log-query/log-query-overview.md)。 大多数部件会包括提供高级可视化效果的标头，以及显示最匹配结果的列表。 每个部件类型提供了 Log Analytics 工作区中记录的不同可视化效果。 在执行获取详细的记录的日志查询的部分中选择元素。 |

## <a name="required-permissions"></a>所需的权限
至少需要[参与者级别权限](manage-access.md#manage-accounts-and-users)要创建或修改视图的 Log Analytics 工作区中。 如果没有此权限，视图设计器选项不会显示在菜单中。


## <a name="work-with-an-existing-view"></a>使用现有视图
使用视图设计器创建的视图显示以下选项：

![概述菜单](media/view-designer/overview-menu.png)

下表描述了这些选项：

| 选项 | 描述 |
|:--|:--|
| 刷新   | 使用最新数据刷新视图。 | 
| 日志      | 此时将打开[Log Analytics](../log-query/portals.md)若要使用日志查询分析数据。 |
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

| 选项 | 描述 |
|:--|:--|
| 保存        | 保存所做的更改并关闭视图。 |
| 取消      | 放弃所做的更改并关闭视图。 |
| 删除视图 | 删除视图。 |
| 导出      | 将视图导出至 [Azure 资源管理器模板](../../azure-resource-manager/resource-group-authoring-templates.md)，可将此模板导入其它工作区。 文件的名称为视图的名称，包含 *omsview* 扩展名。 |
| 导入      | 导入从另一个工作区中导出的 *omsview* 文件。 此操作覆盖现有视图的配置。 |
| 克隆       | 创建一个新视图，并在视图设计器中打开它。 新视图的名称与原始名称相同，但其末尾附加了 *Copy* 字样。 |

## <a name="next-steps"></a>后续步骤
* 将[磁贴](view-designer-tiles.md)添加到自定义视图。
* 将[可视化部件](view-designer-parts.md)添加到自定义视图。
