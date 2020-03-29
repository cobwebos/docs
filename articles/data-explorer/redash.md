---
title: 使用 Redash 可视化 Azure 数据资源管理器
description: 本文介绍如何使用 Redash 本机连接器可视化 Azure 数据资源管理器中的数据。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773955"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>在 Redash 中可视化 Azure 数据资源管理器的数据

[Redash](https://redash.io/) 可以连接和查询数据源，并可以生成仪表板来可视化数据并将其与同事共享。 本文介绍如何将 Azure 数据资源管理器设置为 Redash 的数据源，然后将数据可视化。

## <a name="prerequisites"></a>先决条件

1. [创建群集和数据库](create-cluster-database-portal.md)。
1. 根据[将示例数据引入 Azure 数据资源管理器](ingest-sample-data.md)中所述引入数据。 有关更多引入选项，请参阅[引入概述](ingest-data-overview.md)。

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>在 Redash 中创建 Azure 数据资源管理器连接器 

1. 登录到 [Redash](https://www.redash.io/)。 选择“开始”以创建帐户。****
1. 在“让我们开始吧”中选择“连接数据源”。********

    ![连接数据源](media/redash/connect-data-source.png)

1. 在“创建新数据源”窗口中，依次选择“Azure 数据资源管理器(Kusto)”、“创建”。************ 

    ![选择 Azure 数据资源管理器数据源](media/redash/select-adx-data-source.png)

1. 在“Azure 数据资源管理器(Kusto)”窗口中填写以下表单，然后选择“创建”。********

    ![“Azure 数据资源管理器(Kusto)”设置窗口](media/redash/adx-settings-window.png)

1. 在“设置”窗口中选择“保存”，然后选择“测试连接”以测试“Azure 数据资源管理器(Kusto)”数据源的连接。****************

## <a name="create-queries-in-redash"></a>在 Redash 中创建查询

1. 在 Redash 的左上角，选择 **"创建** > **查询**"。 单击“新建查询”并重命名查询。****

    ![创建查询](media/redash/create-query.png)

1. 在顶部的编辑窗格中键入查询，然后依次选择“保存”、“执行”。******** 选择“发布”以发布查询供将来使用。****

    ![保存并执行查询](media/redash/save-and-execute-query.png)

    在左窗格中，可以在下拉菜单中看到数据源连接名称（在我们的流中为“Github 连接器”），并可以看到所选数据库中的表。**** 

1. 在底部的中间窗格中查看查询结果。 选择“新建可视化效果”按钮创建用于查询的可视化效果。****

    ![新建可视化效果](media/redash/new-visualization.png)

1. 在可视化效果屏幕中选择“可视化效果类型”，以及“X 列”和“Y 列”等相关字段。************ **保存**可视化效果。

    ![配置并保存可视化效果](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>使用参数创建查询

1. **创建** > **查询**以创建新查询。 使用 {{}} 大括号将一个参数添加到查询中。 选择**{}[ ]** 以打开 **"添加参数"** 窗口。 还可以选择“设置”图标来修改现有参数的属性，并打开“<参数名称>”窗口。****** 

    ![插入参数](media/redash/insert-parameter.png)

1. 为参数命名。 选择 **"类型**：**从下拉菜单中选择基于查询的下拉列表**"。 选择“确定”****

    ![基于查询的下拉列表](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 该查询使用多个值，因此必须包含以下语法：`| where Type in ((split('{{Type}}', ',')))`。 有关详细信息，请参阅 [in 运算符](/azure/kusto/query/inoperator)。 这会导致 [Redash 应用中出现多个查询参数选项](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>在 Redash 中创建仪表板

1. 要创建仪表板，**请创建** > **仪表板**。 或者，选择“仪表板”并从列表中选择现有的仪表板。****

    ![创建仪表板](media/redash/create-dashboard.png)

1. 在“新建仪表板”窗口中为仪表板命名，然后选择“保存”。******** 在“<仪表板名称>”窗口中，选择“添加小组件”以创建新的小组件。******** 

1. 在“添加小组件”窗口中选择查询名称，然后选择“选择可视化效果”和“参数”。************ 选择“添加到仪表板”****

   ![选择可视化效果并添加到仪表板](media/redash/add-widget-window.png)

1. 选择“完成编辑”以完成仪表板的创建。****

1.  在仪表板编辑模式下，选择“使用仪表板级别筛选器”以使用前面定义的“类型”参数。********

    ![完成仪表板创建](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>后续步骤

* [为 Azure 数据资源管理器编写查询](write-queries.md)


