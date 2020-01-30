---
title: 通过 Redash 可视化 Azure 数据资源管理器
description: 本文介绍如何将 Azure 数据资源管理器中的数据可视化到 Redash native 连接器。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773955"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>可视化 Redash 中 Azure 数据资源管理器的数据

[Redash](https://redash.io/)连接和查询您的数据源，构建仪表板来可视化数据并将其与对等方共享。 本文介绍如何将 Azure 数据资源管理器设置为 Redash 的数据源，然后将数据可视化。

## <a name="prerequisites"></a>必备组件

1. [创建群集和数据库](create-cluster-database-portal.md)。
1. 引入数据，如将[示例数据引入 Azure 数据资源管理器](ingest-sample-data.md)中所述。 有关引入选项的详细信息，请参阅[引入概述](ingest-data-overview.md)。

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>在 Redash 中创建 Azure 数据资源管理器连接器 

1. 登录到[Redash](https://www.redash.io/)。 选择 "**开始**使用" 创建帐户。
1. 在 "**开始**" 下，选择 "**连接数据源**"。

    ![连接数据源](media/redash/connect-data-source.png)

1. 在 "**新建数据源**" 窗口中，选择 " **Azure 数据资源管理器（Kusto）** "，然后选择 "**创建**"。 

    ![选择 Azure 数据资源管理器数据源](media/redash/select-adx-data-source.png)

1. 在**Azure 数据资源管理器（Kusto）** 窗口中，填写以下表格并选择 "**创建**"。

    ![Azure 数据资源管理器（Kusto）设置窗口](media/redash/adx-settings-window.png)

1. 在 "**设置**" 窗口中，选择 "**保存**并**测试连接**"，测试**Azure 数据资源管理器（Kusto）** 数据源连接。

## <a name="create-queries-in-redash"></a>在 Redash 中创建查询

1. 在 Redash 的左上方，选择 "**创建** > **查询**"。 单击 "**新建查询**"，然后重命名查询。

    ![创建查询](media/redash/create-query.png)

1. 在顶部的编辑窗格中键入查询，然后选择 "**保存**并**执行**"。 选择 "**发布**" 以发布查询以备将来使用。

    ![保存并执行查询](media/redash/save-and-execute-query.png)

    在左侧窗格中，你可以在下拉菜单中看到 "数据源连接名称" （**Github 连接器**）以及所选数据库中的表。 

1. 在底部中央窗格中查看查询结果。 通过选择新的 "**可视化**" 按钮，创建用于查询的可视化效果。

    ![新可视化](media/redash/new-visualization.png)

1. 在可视化效果屏幕中，选择**可视化效果类型**和相关字段，例如**X 列**和**Y 列**。 **保存**视觉对象。

    ![配置并保存可视化](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>使用参数创建查询

1. **创建** > **查询**以创建新查询。 使用 {{}} 大括号向其添加参数。 选择 " **{{}}** " 以打开 "**添加参数**" 窗口。 您还可以选择 "*设置" 图标*来修改现有参数的属性，并打开 " **< parameter_name >** " 窗口。 

    ![插入参数](media/redash/insert-parameter.png)

1. 为参数命名。 从下拉菜单中选择 "**类型**：**基于查询的下拉列表**"。 选择“确定”

    ![基于查询的下拉列表](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 查询使用多个值，因此必须 `| where Type in ((split('{{Type}}', ',')))`中包含以下语法。 有关详细信息，请参阅[in operator](/azure/kusto/query/inoperator)。 这将导致[redash 应用中存在多个查询参数选项](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>在 Redash 中创建仪表板

1. 若要创建仪表板，请**创建** > **仪表板**。 或者，选择 "现有仪表板"、"**仪表**板" > 从列表中选择一个仪表板。

    ![创建仪表板](media/redash/create-dashboard.png)

1. 在 "**新建仪表板**" 窗口中，为仪表板命名并选择 "**保存**"。 在 **< Dashboard_name >** "窗口中，选择"**添加小组件**"以创建新的小组件。 

1. 在 "**添加小组件**" 窗口中，选择查询名称，**选择可视化效果**和**参数**。 选择 "**添加到仪表板**"

   ![选择可视化效果并添加到仪表板](media/redash/add-widget-window.png)

1. 选择 "**完成编辑**" 完成仪表板创建。

1.  在仪表板编辑模式下，选择 "**使用面板级别筛选器**" 以使用之前定义的**类型**参数。

    ![完成仪表板创建](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)


