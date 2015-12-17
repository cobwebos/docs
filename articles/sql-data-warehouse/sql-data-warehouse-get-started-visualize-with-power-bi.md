<properties
    pageTitle="使用 Power BI 可视化 SQL 数据仓库数据 | Microsoft Azure"
    description="使用 Power BI 可视化 SQL 数据仓库数据"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.date="10/21/2015"
    wacn.date=""/>

# 使用 Power BI 可视化数据


> [AZURE.NOTE]若要完成本教程，需要使用一个已随 AdventureWorksDW 示例数据库预先加载的 SQL 数据仓库数据库。[创建 SQL 数据仓库](sql-data-warehouse-get-started-provision.md)说明了如何创建这样一个数据仓库。
> 
> 如果你已有 SQL 数据仓库数据库但没有示例数据，可以[手动加载示例数据][]。

本教程说明如何使用 Power BI 连接到 SQL 数据仓库，并创建一些基本的视觉效果。

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## 连接到 AdventureWorksDW

若要打开 Power BI 并连接到 AdventureWorksDW 数据库，请执行以下操作：

1. 登录到 [Azure 预览门户][]。
2. 单击“SQL 数据库”，然后选择 AdventureWorks SQL 数据仓库数据库。 

    ![查找数据库][1]

3. 单击“在 Power BI 中打开”按钮。

    ![Power BI 按钮][2]

4. 现在，你应会看到显示了数据库 Web 地址的 SQL 数据仓库连接页。单击“下一步”。

    ![Power BI 连接][3]

6. 输入你的 Azure SQL 服务器用户名和密码，然后你将完全连接到 SQL 数据仓库数据库。

    ![Power BI 登录][4]

1. 登录 Power BI 后，单击左侧边栏选项卡上的 AdventureWorksDW 数据集。随后将打开该数据库。

    ![在 Power BI 中打开 AdventureWorksDW][5]



## 创建 Power BI 报告来分析示例数据

现在，你可以使用 Power BI 来分析 AdventureWorksDW 示例数据。为了执行分析，AdventureWorksDW 提供了一个名为 AggregateSales 的视图。此视图包含用于分析公司销售情况的一些关键指标。

1. 若要根据邮政编码创建销售金额图表，请在右侧字段窗格中，单击 AggregateSales 视图将它展开。单击“PostalCode”和“SalesAmount”列以将其选中。 

    ![在 Power BI 中选择 AggregateSales][6]

    Power BI 会自动将此识别为地理数据，并将它放入图中。

    ![Power BI 图表][7]

2. 此步骤将创建条形图，显示针对每个客户的销售收入金额。若要创建此图表，请转到展开的 AggregateSales 视图。单击“SalesAmount”字段。将“客户收入”字段向左拖放到轴中。
    
    ![在 Power BI 中选择轴][8]

    我们已将条形图移到左边。

    ![Power BI 条形图][9]

3. 此步骤将创建一个折线图，显示每个订单日期的销售金额。若要创建此图表，请转到展开的 AggregateSales 视图。单击“SalesAmount”和“OrderDate”。在“视觉效果”列中单击“折线图”图标；这是视觉效果下第二行中的第一个图标。

	![在 Power BI 中选择折线图][10]

    现在已创建了一份报告，其中显示了数据的三种不同视觉效果。

    ![Power BI 折线图][11]

你随时可以单击“文件”，并选择“保存”来保存进度。

## 后续步骤
既然我们提供了一些时间让你尝试处理示例数据，接下来请了解如何[开发][]、[加载][]或[迁移][]数据。

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[迁移]: ./sql-data-warehouse-overview-migrate.md
[开发]: ./sql-data-warehouse-overview-develop.md
[加载]: ./sql-data-warehouse-overview-load.md
[手动加载示例数据]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure 预览门户]: https://manage.windowsazure.cn
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=Mooncake_1207_2015-->