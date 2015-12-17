<properties
   pageTitle="将 Power BI 与 SQL 数据仓库配合使用 | Microsoft Azure"
   description="有关在开发解决方案时将 Power BI 与 Azure SQL 数据仓库配合使用的技巧。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/06/2015"
   wacn.date=""/>

# 将 Power BI 与 SQL 数据仓库配合使用
在 Azure SQL 数据库中，SQL 数据仓库 Direct Connect 可让用户利用功能强大的逻辑下推，以及 Power BI 的分析功能。使用 Direct Connect 可在浏览数据时将查询实时发回到 Azure SQL 数据仓库。此项功能与 SQL 数据仓库的缩放性相结合，可让用户在数分钟内针对 TB 量级的数据创建动态报表。此外，“在 Power BI 中打开”按钮的引入可让用户直接将 Power BI 连接到其 SQL 数据仓库，而无需从其他 Azure 部分收集信息。

使用 Direct Connect 时，请注意：

+ 在连接时指定完全限定的服务器名称（请参阅以下内容以获取详细信息）
+ 确保数据库的防火墙规则设置为“允许访问 Azure 服务”。
+ 每项操作（例如选择列或添加筛选器）将直接查询数据仓库 
+ 大约每隔 15 分钟刷新磁贴一次（不需要计划刷新）
+ 问题解答不适用于 Direct Connect 数据集
+ 不会自动选取架构更改

随着我们持续改进体验，这些限制和说明可能会更改。连接步骤详述如下。

## 使用“在 Power BI 中打开”按钮
使用“在 Power BI 中打开”按钮是在 SQL 数据仓库与 Power BI 之间切换的最简单方式。此按钮可让你顺利地开始在 Power BI 中创建新的仪表板。

1.	若要开始，请导航到 Azure 门户中的 SQL 数据仓库实例。
2.	单击“在 Power BI 中打开”按钮。
3.	如果我们无法直接将你登录，或者你没有 Power BI 帐户，则你需要自行登录。  
4.	你将被定向到 SQL 数据仓库连接页，其中已预先填充了你在 SQL 数据仓库中的信息。
5.  输入凭据后，你将完全连接到 SQL 数据仓库。 

## 通过 Power BI 门户连接
除了使用“在 Power BI 中打开”按钮以外，用户还可以通过 Power BI 门户连接到其 SQL 数据仓库。

1.  在导航窗格的底部单击“获取数据”。
2.  选择“数据库”。
3.  进入“数据库”页后，选择“Azure SQL 数据仓库”，然后单击“连接”。
4.  输入所需的连接信息。下面的“查找参数”部分显示了可在何处找到此数据。 
5.  你将定向回到 Power BI 的主页。在建立连接后，“数据集”下面将出现新的条目，其中包含你的实例名称。  
6.	 你可以单击新的数据集，以浏览数据库中的所有表和视图。选择一个列会将查询发回到源，并动态创建视觉效果。可将这些视觉效果保存在新报表中，并固定回到仪表板。

## 查找参数值
在 Azure 门户中可以找到完全限定的服务器名称和数据库名称。请注意，SQL 数据仓库此时只出现在 Azure 预览门户中。


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Mooncake_1207_2015-->