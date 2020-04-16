---
title: Azure 同步工作室中的 SQL 脚本（预览版）
description: 简介 Azure Synapse 工作室（预览）SQL 脚本
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431067"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>在 Azure 同步工作室中使用 SQL 脚本（预览版）

Azure Synapse 工作室（预览版）提供了一个 SQL 脚本 Web 界面，供您创作 SQL 查询。 您可以连接到 SQL 池（预览）或 SQL 按需（预览）。 

## <a name="begin-authoring-in-sql-script"></a>开始在 SQL 脚本中创作 

有几种方法可以在 SQL 脚本中开始创作体验。 您可以通过以下方法之一创建新的 SQL 脚本。

1. 选择"+"图标并选择 SQL 脚本。

    > [!div class="mx-imgBorder"] 
    >![新脚本](./media/author-sql-script/newsqlscript.png)

2. 从开发 SQL 脚本下的"操作"菜单中从开发 SQL 脚本下的"操作"菜单中选择"新 SQL 脚本"。 

    > [!div class="mx-imgBorder"] 
    > ![新脚本](./media/author-sql-script/newsqlscript2actions.png)

or 

3. 从开发 SQL 脚本下的"操作"菜单中选择"导入"，并从本地存储中选择现有 SQL 脚本。

    > [!div class="mx-imgBorder"] 
    > ![新脚本](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>创建 SQL 脚本

1. 通过选择"属性"按钮并替换分配给 SQL 脚本的默认名称，为 SQL 脚本选择名称。

    > [!div class="mx-imgBorder"] 
    > ![新脚本](./media/author-sql-script/newsqlscriptrename.png)

1. 从"连接到"下拉菜单中选择特定的 SQL 池或按需 SQL。 或者，如有必要，从"使用数据库"中选择数据库。

    > [!div class="mx-imgBorder"] 
    > ![新脚本](./media/author-sql-script/newsqlchoosepool.png)

1. 使用"无意义"功能开始创作 SQL 脚本。

    > [!div class="mx-imgBorder"] 
    > ![新脚本](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>运行 SQL 脚本

选择"运行"按钮以执行 SQL 脚本。 默认情况下，结果将显示在表中。

> [!div class="mx-imgBorder"] 
> ![新脚本](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>导出结果

您可以通过选择"导出结果"并选择扩展，以不同格式（包括 CSV、Excel、JSON、XML）将结果导出到本地存储。

您还可以通过选择"图表"按钮在图表中可视化 SQL 脚本结果。 选择"图表类型"和"类别列"。 您可以通过选择"保存为图像"将图表导出到图片中。 

> [!div class="mx-imgBorder"] 
> ![新脚本](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>从镶木地板文件中浏览数据。

您可以使用 SQL 脚本浏览存储帐户中的 Parquet 文件来预览文件内容。 

> [!div class="mx-imgBorder"] 
> ![新脚本](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 表、外部表、视图

通过选择数据下的"操作"菜单，您可以选择多个操作，如："新 SQL 脚本"、"选择前 1000 行"、"创建"、"创建"和"创建"。 通过右键单击 SQL 池和 SQL 按需节点来浏览可用的手势。

> [!div class="mx-imgBorder"] 
> ![新脚本](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>后续步骤

有关创作 SQL 脚本的详细信息，请参阅[Azure 同步分析](https://docs.microsoft.com/azure/synapse-analytics)。