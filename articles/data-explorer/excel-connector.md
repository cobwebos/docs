---
title: 使用 Microsoft Excel 的 Azure 数据资源管理器连接器进行数据可视化
description: 本文介绍如何使用 Azure 数据资源管理器的 Excel 连接器。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849048"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>使用 Excel 的 Azure 数据资源管理器连接器进行数据可视化

Azure 数据资源管理器提供两个可以在 Excel 中连接到数据的选项：使用本机连接器或从 Azure 数据资源管理器导入查询。 本文介绍如何在 Excel 中使用本机连接器连接到 Azure 数据资源管理器群集以获取并可视化数据。

Azure 数据资源管理器 Excel 本机连接器提供将查询结果导出到 Excel 的功能。 也可将 KQL 查询添加为 Excel 数据源，以便进行其他计算或可视化。

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>将 Kusto 查询定义为 Excel 数据源并将数据加载到 Excel

1. 打开 **Microsoft Excel**。
1. 在“数据”**** 选项卡中，选择“获取数据”**** > “从 Azure”**** > ****“从 Azure 数据资源管理器”。

    ![从 Azure 数据资源管理器获取数据](media/excel-connector/get-data-from-adx.png)

1. 在“Azure 数据资源管理器(Kusto)”**** 窗口中，完成以下字段并选择“确定”。****

    ![“Azure 数据资源管理器(Kusto)”窗口](media/excel-connector/adx-connection-window.png)
    
    |字段   |描述 |
    |---------|---------|
    |**集群**   |   群集的名称（必需）      |    
    |**数据库**     |    数据库的名称      |    
    |**表名或 Azure 数据资源管理器查询**    |     表名或 Azure 数据资源管理器查询    | 
    
    **高级选项：**

     |字段   |描述 |
    |---------|---------|
    |**限制查询结果记录数**     |     限制加载到 Excel 中的记录数  |    
    |**限制查询结果数据大小(字节)**    |    限制数据大小      |   
    |**禁用结果集截断**    |         |      
    |**其他 Set 语句(由分号隔开)**    |    添加要应用到数据源的 `set` 语句     |   

1.  在“导航器”窗格中，导航到正确的表。**** 在表预览窗格中，可以选择“转换数据”对数据进行更改，也可以选择“加载”将其加载到 Excel。********

![表预览窗口](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > 如果已指定“数据库”和/或“表名或 Azure 数据资源管理器查询”，则会自动打开正确的表预览窗格。******** 

## <a name="analyze-and-visualize-data-in-excel"></a>分析和可视化 Excel 中的数据

数据加载到 Excel 并可在 Excel 工作表中使用以后，即可通过创建关系和视觉对象来分析、汇总和可视化数据。 

1.  在“表设计”选项卡中，选择“使用数据透视表进行汇总”。******** 在“创建数据透视表”窗口中，选择相关的表，然后选择“确定”。********

    ![创建数据透视表](media/excel-connector/create-pivot-table.png)

1. 在“数据透视表字段”窗格中，选择相关的表列，以便创建摘要表。**** 在下面的示例中，“EventId”和“状态”已选中。********
    
    ![选择数据透视表字段](media/excel-connector/pivot-table-pick-fields.png)

1. 在“数据透视表分析”选项卡中，选择“数据透视图”，以便创建基于表的视觉对象。******** 

    ![数据透视图](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 在下面的示例中，使用 **Event Id**、**StartTime** 和 **EventType** 来查看有关天气事件的其他信息。

    ![可视化数据](media/excel-connector/visualize-excel-data.png)

1. 创建用于监视数据的完整仪表板。

## <a name="next-steps"></a>后续步骤

[使用导入到 Microsoft Excel 中的 Azure 数据资源管理器 Kusto 查询进行数据可视化](excel-blank-query.md)