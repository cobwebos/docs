<properties
   pageTitle="使用 SQL 数据仓库构建集成解决方案 | Microsoft Azure"
   description="用于集成 SQL 数据仓库的工具以及提供相应解决方案的合作伙伴。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="02/01/2016"
   wacn.date=""/>

#在 SQL 数据仓库中利用其他服务
除了本身的核心功能以外，SQL 数据仓库还允许用户利用 Azure 中的其他许多服务。具体而言，我们目前已采取多种措施深度集成了以下服务：

+ Azure 流分析

我们正努力连接多个跨 Azure 生态系统的服务。


##Azure 流分析
Azure 流分析是复杂、完全托管的基础结构，用于处理和使用从 Azure 事件中心生成的事件数据。通过与 SQL 数据仓库集成，可以有效地处理流数据，并将其与关系数据一起存储以实现更深入、更高级的分析。

+ **作业输出**：将流分析作业的输出直接发送到 SQL 数据仓库。

有关详细信息，请参阅[与 Azure 流分析集成](../sql-data-warehouse-integrate-azure-stream-analytics.md)或 [Azure 流分析文档](/documentation/services/stream-analytics/)。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Mooncake_0307_2016-->