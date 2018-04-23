---
title: 将 Azure 数据工厂与 SQL 数据仓库配合使用 | Microsoft 文档
description: 有关在开发解决方案时将 Azure 数据工厂 (ADF) 与 Azure SQL 数据仓库配合使用的技巧。
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: ''
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>将 Azure 数据工厂与 SQL 数据仓库配合使用
Azure 数据工厂提供完全托管的方法，可以协调数据传输和 SQL 数据仓库上存储过程的执行。  这样，便可以更轻松地设置和计划 SQL 数据仓库的复杂提取、转换和加载 (ETL) 过程。 有关 Azure 数据工厂更完整的概述，请参阅 [Azure 数据工厂文档][Azure Data Factory documentation]。

## <a name="data-movement"></a>数据移动
使用 Azure 数据工厂可以在本地源与不同 Azure 服务之间移动数据。  总体而言，当前与 Azure 数据工厂的集成支持在以下位置之间双向移动数据：

* Azure Blob 存储
* Azure SQL 数据库
* 本地 SQL Server
* IaaS 上的 SQL Server

若要了解如何设置数据复制活动，请参阅[使用 Azure 数据工厂复制数据][Copy data with Azure Data Factory]

## <a name="stored-procedures"></a>存储过程
 除了用于计划数据传输，Azure 数据工厂还可用于协调存储过程的执行。  这便可以创建更复杂的管道并扩展 Azure 数据工厂的功能，以利用 SQL 数据仓库的计算能力。

## <a name="next-steps"></a>后续步骤
有关集成的概述，请参阅 [SQL 数据仓库集成概述][SQL Data Warehouse integration overview]。
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述][SQL Data Warehouse development overview]。

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

