<properties
   pageTitle="将 Azure 数据工厂与 SQL 数据仓库配合使用 | Microsoft Azure"
   description="有关在开发解决方案时将 Azure 数据工厂 (ADF) 与 Azure SQL 数据仓库配合使用的技巧。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="05/02/2016"
   wacn.date=""/>

# 将 Azure 数据工厂与 SQL 数据仓库配合使用

Azure 数据工厂提供完全托管的方法，可以协调数据传输和 SQL 数据仓库上存储过程的执行。这样，你就可以更轻松地设置和计划 SQL 数据仓库的复杂提取、转换和加载 (ETL) 过程。有关 Azure 数据工厂的更完整概述，请参阅 [Azure 数据工厂文档][]。

## 数据移动

使用 Azure 数据工厂可以在本地源与不同 Azure 服务之间移动数据。总体而言，当前与 Azure 数据工厂的集成支持在以下位置之间双向移动数据：

+ Azure Blob 存储
+ Azure SQL 数据库
+ 本地 SQL Server
+ IaaS 上的 SQL Server

有关如何设置数据复制活动的信息，请参阅[使用 Azure 数据工厂复制数据][]

## 存储过程
 除了用于计划数据传输，Azure 数据工厂还可用于协调存储过程的执行。这便可以创建更复杂的管道并扩展 Azure 数据工厂的功能，以利用 SQL 数据仓库的计算能力。

## 后续步骤
有关集成的概述，请参阅 [SQL 数据仓库集成概述][]。
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述][]。

<!--Image references-->

<!--Article references-->

[使用 Azure 数据工厂复制数据]: /documentation/articles/data-factory-azure-sql-connector/
[SQL 数据仓库开发概述]: /documentation/articles/sql-data-warehouse-overview-develop/
[SQL 数据仓库集成概述]: /documentation/articles/sql-data-warehouse-overview-integrate/

<!--MSDN references-->

<!--Other Web references-->
[Azure 数据工厂文档]: https://azure.microsoft.com/documentation/services/data-factory/


<!---HONumber=Mooncake_0606_2016-->