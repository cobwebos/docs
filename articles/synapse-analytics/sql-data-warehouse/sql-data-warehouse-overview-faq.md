---
title: Azure Synapse Analytics（前称为 SQL 数据仓库）常见问题解答
description: 本文列出了客户和开发人员提出的有关 Azure Synapse Analytics（前称为 SQL 数据仓库）的常见问题的解答
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 766615a92e616a254cca4b2b3ce1ccde40118275
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350250"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics（前称为 SQL 数据仓库）常见问题解答

## <a name="general"></a>常规

Q. 什么是 Azure Synapse？

A. Azure Synapse 是一种无限制的分析服务，它将数据仓库和大数据分析结合在一起。 它让你可以根据自己的条件自由查询数据，无论是使用无服务器的按需资源还是使用大规模预配的资源。 Azure Synapse 将这两个世界与统一体验相结合，可进行摄用、准备、管理和服务数据，以满足即时 BI 和机器学习需求。 有关详细信息，请参阅[什么是 Azure 突触分析](sql-data-warehouse-overview-what-is.md)。

Q. Azure SQL 数据仓库有什么变化？

A. Azure Synapse 是 Azure SQL 数据仓库 (SQL DW) 的演进。 我们将同行业领先的数据仓库带到了全新的性能和功能级别。 您可以使用 Azure Synapss 继续在生产中运行现有数据仓库工作负荷，并自动受益于预览版中的新功能。 有关详细信息，请参阅什么是[Azure 突触分析](sql-data-warehouse-overview-what-is.md)。

Q. 什么是 SQL Analytics？

A. SQL Analytics 是 Azure Synapse 中正式发布的企业数据仓库功能。 有关详细信息，请参阅[什么是 Azure 突触分析](sql-data-warehouse-overview-what-is.md)。

Q. Azure Synapse 如何入门？

A. 您可以开始使用 Azure[免费帐户](https://azure.microsoft.com/free/sql-data-warehouse/)或[联系销售人员了解更多信息](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)。 

Q. Azure Synapse 提供哪些功能来确保数据安全性？

A. Azure Synapse 提供多种解决方案来保护数据，例如 TDE 和审核。 有关详细信息，请参阅[安全性](sql-data-warehouse-overview-manage-security.md)。

Q. 从何处可以查明 Azure Synapse 符合哪些法律或企业标准？

A. 请访问 [Microsoft 符合性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)页面，查明产品（如 SOC 和 ISO）的各种符合性规定。 首先，按合规标题进行选择。 然后在页面右侧的 Microsoft 范围云服务部分中展开 Azure，以查看哪些服务符合 Azure Synapse 要求。

Q. 是否可以连接 Power BI？

A.  能！ 尽管 Power BI 支持使用 Azure 突触直接查询，但它不适用于大量用户或实时数据。 若要进一步优化 Power BI 性能，请考虑在 Azure Analysis Services 或 Analysis Service IaaS 的顶层使用 Power BI。

Q. SQL Analytics 容量有哪些限制？

A. 请参阅当前[容量限制](sql-data-warehouse-service-capacity-limits.md)页。 

Q. 为什么缩放/暂停/恢复会花费很长时间？

A. 有几个因素会影响计算管理操作的时间。 长时间运行操作的常见情况是事务回滚。 缩放或暂停操作启动时，会阻止所有传入会话和查询。 为了使系统处于稳定状态，必须在操作开始前回退事务。 事务数量越多，其日志大小越大，使系统恢复到稳定状态的操作耗时越久。

## <a name="user-support"></a>用户支持

Q. 在何处提交功能请求？

A. 如果有功能请求，请在 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 页上提交

Q. 该如何操作？

A. 有关使用 Azure 突触进行开发的帮助，您可以在[我们的堆栈溢出](https://stackoverflow.com/questions/tagged/azure-sqldw)页上提问。 

Q. 如何提交支持票证？

A. 可通过 Azure 门户提交[支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="sql-languagefeature-support"></a>SQL 语言/功能支持 

Q. 支持哪些数据类型？

A. 请参阅[数据类型](sql-data-warehouse-tables-data-types.md)。

Q. 支持哪些表功能？

A. 支持许多功能。 不支持的功能可以在[不支持的表功能](sql-data-warehouse-tables-data-types.md)中找到。

## <a name="tooling-and-administration"></a>工具和管理

Q. SQL Analytics 是否支持 REST API？

A. 是的。 SQL Analytics 还提供可与 SQL 数据库搭配使用的大多数 REST 功能。 可以在 REST 文档页或 [MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx) 中找到 API 信息。


## <a name="loading"></a>加载

Q. 支持哪些客户端驱动程序？

A. 可在[连接字符串](sql-data-warehouse-connection-strings.md)页面上找到对 SQL 分析的驱动程序支持

问：PolyBase 支持哪些文件格式？

答：Orc、RC、Parquet 和平面分隔文本

问：可以使用 PolyBase 连接到哪些数据源？ 

答[：Azure 数据湖存储](sql-data-warehouse-load-from-azure-data-lake-store.md)和[Azure 存储 Blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

问：连接到 Azure 存储 Blob 或 ADLS 时，计算是否可以向下？ 

答：否，PolyBase 仅与存储组件交互。 

问：能否连接到 HDI？

答：HDI 可使用 ADLS 或 WASB 作为 HDFS 层。 如果具有 HDFS 层，则可以将数据加载到 SQL Analytics 数据仓库中。 但是，无法生成 HDI 实例的下推计算。 

## <a name="next-steps"></a>后续步骤
有关 Azure Synapse 的综合性详细信息，请参阅[概述](sql-data-warehouse-overview-faq.md)页。
