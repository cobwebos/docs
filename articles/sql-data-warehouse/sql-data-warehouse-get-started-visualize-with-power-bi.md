---
title: "实现与 Power BI Microsoft Azure SQL 数据仓库数据可视化效果"
description: "使用 Power BI 可视化 SQL 数据仓库数据"
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: a41393730143b14e91318a61858d989fff3786c1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="visualize-data-with-power-bi"></a>使用 Power BI 可视化数据
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

本教程演示如何使用 Power BI 连接到 SQL 数据仓库并创建几个基本的视觉效果。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>先决条件
若要逐步完成本教程，你需要：

* SQL 数据仓库预先加载了 AdventureWorksDW 数据库。 若要设置此，请参阅[创建 SQL 数据仓库][ Create a SQL Data Warehouse] ，并选择加载示例数据。 如果你已有数据仓库，但没有示例数据，则可以[手动加载示例数据][load sample data manually]。

## <a name="1-connect-to-your-database"></a>1.连接到数据库
若要打开 Power BI 并连接到 AdventureWorksDW 数据库：

1. 登录到[Azure 门户][Azure portal]。
2. 单击**SQL 数据库**，然后选择 AdventureWorks SQL 数据仓库数据库。
   
    ![查找你的数据库][1]
3. 单击在 Power BI 中打开按钮。
   
    ![Power BI 按钮][2]
4. 你现在应看到 SQL 数据仓库连接页，其中显示你数据库的 web 地址。 单击下一步。
   
    ![Power BI 连接][3]
5. 输入你的 Azure SQL server 用户名和密码，然后你将完全连接到你的 SQL 数据仓库数据库。
   
    ![Power BI 登录][4]
6. 一旦你已登录到 Power BI 中，单击左侧边栏选项卡上的 AdventureWorksDW 数据集。 这将打开数据库。
   
    ![Power BI 中打开 AdventureWorksDW][5]

## <a name="2-create-a-report"></a>2.创建报表
现在你就可以使用 Power BI 来分析 AdventureWorksDW 示例数据。 若要执行分析，AdventureWorksDW 提供了一个名为 AggregateSales 视图。 此视图包含几个用于分析公司销售的关键指标。

1. 若要创建的邮政编码，根据销售额的映射在右侧字段窗格中，单击 AggregateSales 视图，以将其展开。 单击 PostalCode 和 SalesAmount 列，以将它们选中。
   
    ![Power BI 中选择 AggregateSales][6]
   
    Power BI 会自动识别这是地理数据，为你将其放入一个映射。
   
    ![Power BI 地图][7]
2. 此步骤将创建一个条形图，显示的每个客户收入的销售额。 若要创建此，请转到已展开的 AggregateSales 视图。 单击 SalesAmount 字段。 向左拖动客户收入字段拖放到轴。
   
    ![Power BI 中选择轴][8]
   
    我们移到左侧的条形图。
   
    ![Power BI 条形图][9]
3. 此步骤将创建一个线图，显示每个订单日期的销售金额。 若要创建此，请转到已展开的 AggregateSales 视图。 单击 SalesAmount 和 OrderDate。 在可视化效果中列中，单击折线图图标;这是视觉效果下第二个行中的第一个图标。
   
    ![Power BI 中选择折线图][10]
   
    你现在具有一个报表来显示数据的三个不同的可视化效果。
   
    ![Power BI 折线图][11]

你可以随时通过单击保存你的进度**文件**并选择**保存**。

## <a name="next-steps"></a>后续步骤
现在，我们提供了你一些时间来预热使用示例数据，请参阅如何[开发][develop]，[加载][load]，或[迁移][migrate]。 看一看或[Power BI 网站][Power BI website]。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure portal]: https://portal.azure.com/
[Power BI website]: http://www.powerbi.com/
