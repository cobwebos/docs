---
title: 使用 SQL 数据仓库构建集成解决方案 | Microsoft 文档
description: '用于集成 SQL 数据仓库的工具以及提供相应解决方案的合作伙伴。 '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>使用 SQL 数据仓库集成其他服务
除了本身的核心功能以外，SQL 数据仓库还允许用户集成 Azure 中的其他许多服务。 这些服务包括：

* Power BI
* Azure 数据工厂
* Azure 机器学习
* Azure 流分析

SQL 数据仓库继续集成 Azure 中的更多服务，以及更多的[集成合作伙伴](sql-data-warehouse-partner-data-integration.md)。

## <a name="power-bi"></a>Power BI
借助 Power BI 集成，可以结合 SQL 数据仓库的计算能力以及 Power BI 的动态报告和视觉效果。 Power BI 集成当前包括：

* **直接连接**：使用逻辑下推与 SQL 数据仓库建立更高级的连接。 下推提供更快且更大规模的分析。
* **在 Power BI 中打开**：“在 Power BI 中打开”按钮将实例信息传递给 Power BI，以简化连接方式。

有关详细信息，请参阅[与 Power BI 集成](sql-data-warehouse-get-started-visualize-with-power-bi.md)或 [Power BI 文档](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)。

## <a name="azure-data-factory"></a>Azure 数据工厂
Azure 数据工厂为用户提供一个托管平台，用于创建复杂的提取和加载管道。 SQL 数据仓库与 Azure 数据工厂的集成包括：

* **存储过程**：协调 SQL 数据仓库上存储过程的执行。
* **复制**：使用 ADF 将数据移动到 SQL 数据仓库。 实际上，此操作可以使用 ADF 标准数据移动机制或封面下的 PolyBase。 

有关详细信息，请参阅[与 Azure 数据工厂集成](sql-data-warehouse-get-started-visualize-with-power-bi.md)。

## <a name="azure-machine-learning"></a>Azure 机器学习
Azure 机器学习是完全托管的分析服务，因此你可以使用大量预测工具创建复杂的模型。 支持将 SQL 数据仓库用作模型的源和目标，这些模型具有以下功能：

* **读取数据**：使用 T-SQL 针对 SQL 数据仓库大规模驱动模型。
* **写入数据**：将任一模型中的更改提交回到 SQL 数据仓库。

有关详细信息，请参阅[与 Azure 机器学习集成](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)。

## <a name="azure-stream-analytics"></a>Azure 流分析
Azure 流分析是复杂、完全托管的基础结构，用于处理和使用从 Azure 事件中心生成的事件数据。  通过与 SQL 数据仓库集成，可以有效地处理流数据，并将其与关系数据一起存储以实现更深入、更高级的分析。  

* **作业输出**：将流分析作业的输出直接发送到 SQL 数据仓库。

有关详细信息，请参阅[与 Azure 流分析集成](sql-data-warehouse-integrate-azure-stream-analytics.md)。

## <a name="next-steps"></a>后续步骤
要与 Azure SQL 数据库集成，请参阅[配置 SQL 数据库弹性查询](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

