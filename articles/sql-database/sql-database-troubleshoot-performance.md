<properties
	pageTitle="如何评估和改进 Azure SQL 数据库中的数据库性能"
	description="描述如何评估和改进数据库性能"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/03/2016"
	ms.author="v-shysun"/>

# 如何评估和改进 Azure SQL 数据库中的数据库性能
你随时可以更改单一数据库的[服务层](/documentation/articles/sql-database-service-tiers/)或增加弹性数据库池的 eDTU 以提高性能，但首先可能需要确认提升并优化查询性能的机会。缺少索引与查询优化不足是数据库性能不佳的常见原因。

[AZURE.INCLUDE [support-disclaimer](../includes/support-disclaimer.md)]

## 评估和优化数据库性能的步骤
1.	在 [Azure 门户](https://portal.azure.cn)中，单击“SQL 数据库”，选择该数据库，然后使用“监视”图表查找接近其上限的资源。默认将显示 DTU 消耗量。单击“编辑”更改显示的时间范围和值。
2.	使用 [Query Performance Insight](/documentation/articles/sql-database-query-performance/) 评估使用 DTU 的查询，然后使用 [SQL 数据库顾问](/documentation/articles/sql-database-index-advisor/)查看用于创建和删除索引、参数化查询以及解决架构问题的建议。
3.	可以使用动态管理视图 (DMV)、扩展事件 (Xevent) 和 SSMS 中的查询存储实时获取性能参数。有关详细的监视和优化提示，请参阅[性能指南主题](/documentation/articles/sql-database-performance-guidance/)。

## 使用更多资源提高数据库性能的步骤
1.	对于单一数据库，你可以根据需要[更改服务层](/documentation/articles/sql-database-scale-up/)以提高数据库性能。
2.	对于多个数据库，请考虑使用[弹性数据库池](/documentation/articles/sql-database-elastic-pool-guidance/)自动调整资源规模。

<!---HONumber=Mooncake_0718_2016-->