<properties
   pageTitle="将示例数据载入 SQL 数据仓库 | Microsoft Azure"
   description="了解如何将示例数据载入 SQL 数据仓库"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="10/21/2015"
   wacn.date=""/>

# 将示例数据载入 SQL 数据仓库

在创建 SQL 数据仓库实例时，你可以轻松地将某些示例数据载入其中。如果在预配期间错过了此步骤，你也可以[手动加载示例数据][]。

下面简要介绍如何在数据库中加载 AdventureWorksDW。此数据集以一家虚构公司（称为 AdventureWorks）的示例数据仓库结构为模型，其中包含代表该公司销售与客户情况的数据。

## 在创建过程中添加示例数据
你可以使用以下步骤，确保在部署期间将示例数据载入 SQL 数据仓库：

1. 要启动创建过程，请在 [Azure 门户][]中依次单击“+ 新建”与“数据和存储”，或者在应用商店中搜索“SQL 数据仓库”，以找到 SQL 数据仓库。 
 
2. 启动该过程后，请确保单击“选择源”选项并将它设置为“示例”。如果你不是在创建新的服务器，系统还会要求你提供用于创建过程的服务器的登录名。


> [AZURE.NOTE]若要将示例数据载入实例，需要允许 Azure 服务访问你的服务器（在创建新的服务器时，应该默认已启用此项）。如果未执行此操作，加载将会失败，但你仍能[手动加载示例数据][]。


## 使用 Power BI 分析 Adventureworks

使用示例数据集是操作 Power BI 的好方法。加载示例数据后，可以打开与 SQL 数据仓库的连接，方法是在 Azure 门户中单击“在 Power BI 中打开”按钮，或者转到 [Power BI][] 并[连接到 SQL 数据仓库][]。连接后，应该使用与数据仓库相同的名称来创建新的数据集。为了方便分析，我们创建了名为“AggregateSales”的视图，以及多个可用于分析公司销售的关键度量值。你可以单击此视图的名称来展开视图，并查看其中包含的列，并且可遵循以下步骤创建一些快速视觉效果：

1. 若要开始，我们可以单击“PostalCode”和“SalesAmount”列，轻松创建所有销售活动的图。Power BI 甚至会自动将此识别为地理数据，并将它放入图中。 

2. 现在，如果想要创建销售条形图，只要单击“SalesAmount”列，Power BI 将为你自动创建。可通过将“CustomerIncome”图表拖放到“AggregateSales”左边的“轴”字段来增加额外的深度，以便按客户营收等级显示销售业绩。

3. 最后，如果想要创建销售时间轴，只需单击“SalesAmount”、“OrderDate”和“折线图”（“视觉效果”下第二行中的第一个图标）即可。

随时可以通过单击左下角的“保存”按钮并将视觉效果保存为报告，来保存你的进度。

## 连接并查询示例

你也可以使用传统的方式来分析示例数据。如[连接和查询][]文档中所述，你可以使用 Visual Studio 中的 SQL Server Data Tools 连接到此数据库。将一些示例数据载入 SQL 数据仓库后，你可以快速运行几个查询来开始处理。

我们可以运行一个简单的 select 语句来获取员工的所有信息：

	SELECT * FROM DimEmployee;

我们还可以使用构造（例如 GROUP BY）运行更复杂的查询，来查看每天所有销售活动的总金额：

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

我们甚至可以使用 WHERE 子句来筛选出特定日期之前的订单：

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

事实上，SQL 数据仓库几乎支持 SQL Server 所支持的全部 T-SQL 构造，两者的一些差异可以在[迁移代码][]文档中找到。



## 后续步骤
既然我们提供了一些时间让你尝试处理示例数据，接下来请了解如何[开发][]、[加载][]或[迁移][]数据。

<!--Image references-->

<!--Article references-->
[迁移]: ./sql-data-warehouse-overview-migrate.md
[开发]: ./sql-data-warehouse-overview-develop.md
[加载]: ./sql-data-warehouse-overview-load.md
[连接和查询]: ./sql-data-warehouse-get-started-connect.md
[迁移代码]: ./sql-data-warehouse-migrate-code.md
[手动加载示例数据]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure 门户]: https://manage.windowsazure.cn
[Power BI]: http://www.powerbi.com/
[连接到 SQL 数据仓库]: ./sql-data-warehouse-integrate-power-bi.md

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=Mooncake_1207_2015-->