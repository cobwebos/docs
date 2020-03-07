---
title: 什么是 Azure Synapse Analytics（以前称为 SQL DW）？
description: Azure Synapse Analytics（以前称为 SQL DW）是一种无限制的分析服务，它将企业数据仓库和大数据分析结合在一起。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 68d39b4f363794d50fd05c2067502fc55d5d0170
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380706"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>什么是 Azure Synapse Analytics（以前称为 SQL DW）？

Azure Synapse 是一种无限制的分析服务，它将企业数据仓库和大数据分析结合在一起。 借助它可以使用无服务器的按需资源或预配资源，任意执行自己定义的大规模数据查询。 Azure Synapse 将这两个领域紧密结合，以统一的经验引入、准备、管理和服务数据，满足即时 BI 和机器学习的需求

Azure Synapse 包含四个组件：
- SQL Analytics：基于 T-SQL 的完整分析 - 正式发布
    - SQL 池（按预配的 DWU 付费） 
    - SQL 随选（按处理的 TB 付费）-（预览）
- Spark：深度集成的 Apache Spark（预览） 
- 数据集成：混合数据集成（预览）
- 工作室：统一的用户体验。  （预览版）

> [!NOTE]
> 要访问 Azure Synapse 的预览功能，请在[此处 请求访问](https://aka.ms/synapsepreview)。 Microsoft 将对所有请求进行会审，并尽快做出响应。

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Azure Synapse 中的 SQL Analytics 和 SQL 池

SQL Analytics 是指 Azure Synapse 中正式发布的企业数据仓库功能。 

SQL 池表示使用 SQL Analytics 时预配的分析资源集合。 SQL 池的大小由数据仓库单位 (DWU) 决定。

使用简单的 [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) T-SQL 查询导入大数据，然后利用 MPP 的功能运行高性能分析。 进行集成和分析时，SQL Analytics 是企业为了获取更快且更可靠见解能够依赖的唯一事实来源。  

## <a name="key-component-of-a-big-data-solution"></a>大数据解决方案的关键组件

数据仓库是基于云的端到端大数据解决方案的关键组件。

![数据仓库解决方案](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

在云数据解决方案中，可从各种源将数据引入大数据存储中。 将数据置于大数据存储中以后，Hadoop、Spark 和机器学习算法就可以准备和训练数据。 当数据准备就绪可以进行复杂的分析时，SQL Analytics 就会使用 PolyBase 来查询大数据存储。 PolyBase 使用标准 T-SQL 查询将数据引入 SQL Analytics 表中。
 
SQL Analytics 通过按列存储将数据存储到关系表中。 此格式可显著降低数据存储费用，改进查询性能。 存储数据后，即可大规模地运行分析。 与传统数据库系统相比，数分钟的分析查询只需数秒即可完成，数天的查询只需数小时。 

分析结果可以传输到世界各地的报告数据库或应用程序。 然后即可通过业务分析获得进行明智的业务决策所需的见解。

## <a name="next-steps"></a>后续步骤

- 浏览 [Azure Synapse 体系结构](massively-parallel-processing-mpp-architecture.md)
- 快速[创建 SQL 池](create-data-warehouse-portal.md)
- [加载示例数据](sql-data-warehouse-load-sample-databases.md)
- 浏览[视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

或者，查看以下一些其他 Azure Synapse 资源。  
* 搜索[博客](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* 提交[功能请求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)
* 搜索 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* 搜索 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)