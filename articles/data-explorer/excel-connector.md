---
title: 使用 Microsoft Excel 的 Azure 数据资源管理器连接器实现数据的可视化效果
description: 本文介绍如何使用 Excel connector for Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849048"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>使用适用于 Excel 的 Azure 数据资源管理器连接器实现数据的可视化效果

Azure 数据资源管理器提供两个选项用于连接到 Excel 中的数据：使用本机连接器或从 Azure 数据资源管理器导入查询。 本文介绍如何使用 Excel 中的本机连接器并连接到 Azure 数据资源管理器群集来获取和可视化数据。

Azure 数据资源管理器 Excel 本机连接器提供将查询结果导出到 Excel 的功能。 您还可以将 KQL 查询添加为 Excel 数据源，以便进行其他计算或可视化。

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>将 Kusto 查询定义为 Excel 数据源，并将数据加载到 Excel

1. 打开 **Microsoft Excel**。
1. 在 "**数据**" 选项卡中，选择 "**从**azure > **从 azure 数据资源管理器** **获取数据** > "。

    ![从 Azure 数据资源管理器获取数据](media/excel-connector/get-data-from-adx.png)

1. 在 " **Azure 数据资源管理器（Kusto）** 窗口中，填写以下字段并选择 **" 确定 "** 。

    ![Azure 数据资源管理器（Kusto）窗口](media/excel-connector/adx-connection-window.png)
    
    |字段   |描述 |
    |---------|---------|
    |**Cluster**   |   群集名称（必需）      |    
    |**数据库**     |    数据库的名称      |    
    |**表名或 Azure 数据资源管理器查询**    |     表或 Azure 数据资源管理器查询的名称    | 
    
    **高级选项：**

     |字段   |描述 |
    |---------|---------|
    |**限制查询结果记录号**     |     限制加载到 excel 中的记录数  |    
    |**限制查询结果数据大小（字节）**    |    限制数据大小      |   
    |**禁用结果集截断**    |         |      
    |**附加 Set 语句（用分号分隔）**    |    添加要应用于数据源的 `set` 语句     |   

1.  在 "**导航器**" 窗格中，导航到正确的表。 在 "表预览" 窗格中，选择 "**转换数据**" 以更改数据，或选择 "**加载**" 以将数据加载到 Excel 中。

![表预览窗口](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > 如果已指定**数据库**和/或**表名或 Azure 数据资源管理器查询**，则正确的表预览窗格将自动打开。 

## <a name="analyze-and-visualize-data-in-excel"></a>在 Excel 中分析和可视化数据

在将数据加载到 excel 并在 Excel 工作表中使用后，可以通过创建关系和视觉对象来分析、汇总和可视化数据。 

1.  在 "**表设计**" 选项卡中，选择 "**汇总数据透视表**"。 在 "**创建数据透视表**" 窗口中，选择相关表，然后选择 **"确定"** 。

    ![创建数据透视表](media/excel-connector/create-pivot-table.png)

1. 在 "**数据透视表字段**" 窗格中，选择相关表列以创建摘要表。 在下面的示例中，选择了**EventId**和**状态**。
    
    ![选择数据透视表字段](media/excel-connector/pivot-table-pick-fields.png)

1. 在 "**数据透视表分析**" 选项卡中，选择 "**数据透视图**" 创建基于表的视觉对象。 

    ![数据透视图](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 在下面的示例中，使用事件 Id、 **StartTime**和**事件 Id** **来查看**有关天气事件的其他信息。

    ![可视化数据](media/excel-connector/visualize-excel-data.png)

1. 创建完整仪表板来监视数据。

## <a name="next-steps"></a>后续步骤

[使用导入到 Microsoft Excel 中的 Azure 数据资源管理器 Kusto 查询来可视化数据](excel-blank-query.md)