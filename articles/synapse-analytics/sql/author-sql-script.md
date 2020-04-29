---
title: Azure Synapse Studio 中的 SQL 脚本（预览版）
description: 介绍 Azure Synapse Studio （预览版） SQL 脚本
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431067"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>使用 Azure Synapse Studio 中的 SQL 脚本（预览版）

Azure Synapse Studio （预览版）提供了一个用于编写 SQL 查询的 SQL 脚本 web 界面。 可以连接到 SQL 池（预览版）或 SQL 点播（预览版）。 

## <a name="begin-authoring-in-sql-script"></a>开始在 SQL 脚本中创作 

可以通过多种方法在 SQL 脚本中启动创作体验。 可以通过以下方法之一创建新的 SQL 脚本。

1. 选择 "+" 图标，然后选择 "SQL 脚本"。

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. 从 "开发 SQL 脚本" 下的 "操作" 菜单中，从 "开发 SQL 脚本" 下的 "操作" 菜单中选择 "新建 SQL 脚本"。 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

或 

3. 从 "开发 SQL 脚本" 下的 "操作" 菜单中选择 "导入"，并从本地存储中选择现有的 SQL 脚本。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>创建 SQL 脚本

1. 通过选择 "属性" 按钮并替换分配给 SQL 脚本的默认名称，为你的 SQL 脚本选择一个名称。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. 从 "连接到" 下拉菜单中选择特定 SQL 池或 SQL 点播。 如果需要，请从 "使用数据库" 中选择数据库。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. 开始使用 intellisense 功能创作你的 SQL 脚本。

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>运行 SQL 脚本

选择 "运行" 按钮以执行您的 SQL 脚本。 默认情况下，表中会显示结果。

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>导出结果

可以通过选择 "导出结果" 并选择相应的扩展，将结果以不同的格式（包括 CSV、Excel、JSON、XML）导出到本地存储中。

还可以通过选择 "图表" 按钮，将图表中的 SQL 脚本结果可视化。 选择 "图表类型" 和 "类别列"。 可以通过选择 "另存为图像" 将图表导出到图片中。 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>浏览 Parquet 文件中的数据。

可以使用 SQL 脚本浏览存储帐户中的 Parquet 文件，以预览文件内容。 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 表、外部表、视图

通过选择 "数据" 下的 "操作" 菜单，可以选择多个操作，例如： "新建 SQL 脚本"、"选择前1000行"、"创建"、"删除和创建"。 通过右键单击 SQL 池和 SQL 点播节点上的节点来浏览可用的手势。

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>后续步骤

有关编写 SQL 脚本的详细信息，请参阅[Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)。