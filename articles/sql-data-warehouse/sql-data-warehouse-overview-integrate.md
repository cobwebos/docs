---
title: 构建集成解决方案
description: 与使用 SQL Analytics 预配的数据仓库集成的解决方案工具和合作伙伴。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8b38b50401c50ecfb9cd37c53ad013ca05e126c0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153292"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>将其他服务与 SQL Analytics 数据仓库集成 
Azure Synapse Analytics 中的 SQL Analytics 功能使用户能够与 Azure 中的其他许多服务集成。 使用 SQL Analytics，可以通过其 SQL 池资源创建数据仓库，然后可以使用几个附加服务，其中一些服务包括：

* Power BI
* Azure 数据工厂
* Azure 机器学习
* Azure 流分析

有关跨 Azure 的 integration services 的详细信息，请查看[集成合作伙伴](sql-data-warehouse-partner-data-integration.md)一文。

## <a name="power-bi"></a>Power BI
Power BI 集成，你可以将数据仓库的计算能力与 Power BI 的动态报表和可视化效果结合起来。 Power BI 集成当前包括：

* **直接连接**：使用逻辑下推与使用 SQL 池预配的数据仓库建立更高级的连接。 下推提供更快且更大规模的分析。
* **在 Power BI 中打开**： "在 Power BI 中打开" 按钮将实例信息传递给 Power BI，以简化连接方式。

有关详细信息，请参阅[与 Power BI 集成](sql-data-warehouse-get-started-visualize-with-power-bi.md)或 [Power BI 文档](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)。

## <a name="azure-data-factory"></a>Azure 数据工厂
Azure 数据工厂为用户提供一个托管平台，用于创建复杂的提取和加载管道。 SQL 池与 Azure 数据工厂的集成包括：

* **存储过程**：协调存储过程的执行。
* **Copy**：使用 ADF 将数据移到 SQL 池中。 实际上，此操作可以使用 ADF 标准数据移动机制或封面下的 PolyBase。 

有关详细信息，请参阅[与 Azure 数据工厂集成](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json)。

## <a name="azure-machine-learning"></a>Azure 机器学习
Azure 机器学习是完全托管的分析服务，因此你可以使用大量预测工具创建复杂的模型。 同时支持 SQL 池作为这些模型的源和目标，并具有以下功能：

* **读取数据：** 使用 T-sql 针对 SQL 池大规模驱动模型。
* **写入数据：** 将任何模型中的更改提交回 SQL 池。

有关详细信息，请参阅[与 Azure 机器学习集成](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)。

## <a name="azure-stream-analytics"></a>Azure 流分析
Azure 流分析是复杂、完全托管的基础结构，用于处理和使用从 Azure 事件中心生成的事件数据。  与 SQL 池集成允许有效处理数据，并将其与关系数据一起存储，以实现更深入、更高级的分析。  

* **作业输出：** 将流分析作业的输出直接发送到 SQL 池中。

有关详细信息，请参阅[与 Azure 流分析集成](sql-data-warehouse-integrate-azure-stream-analytics.md)。


