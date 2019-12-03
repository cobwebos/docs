---
title: Azure Synapse Analytics （以前称为 SQL DW）常见问题
description: 本文列出了来自客户和开发人员的 Azure Synapse Analytics （以前称为 SQL DW）的常见问题
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9e64d184ef0fd120d1bc64ce274ee882b7938df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708653"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics （以前称为 SQL DW）常见问题

## <a name="general"></a>一般信息

问： 什么是 Azure Synapse？

A. Azure Synapse 是一个无限制的分析服务，它将数据仓库和大数据分析集中在一起。 它可让你随意使用无服务器点播或预配的资源按比例查询数据。 Azure Synapse 将这两个领域紧密结合，以统一的经验引入、准备、管理和服务数据，满足即时 BI 和机器学习的需求。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： Azure SQL 数据仓库发生了什么情况？

A. Azure Synapse 是 Azure SQL 数据仓库（SQL DW）的发展。 我们将同一行业领先的数据仓库与全新的性能和功能级别一起使用。 现在，可以通过 Azure Synapse 继续在生产环境中运行现有数据仓库工作负荷，并可通过预览版中的新功能自动受益。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： SQL Analytics 是什么？

A. SQL Analytics 是 Azure Synapse 中正式发布的企业数据仓库功能。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： 如何实现 Azure Synapse 入门？

A. 若要[了解详细信息](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)，可以开始使用[Azure 免费帐户](https://azure.microsoft.com/free/sql-data-warehouse/)或联系销售额。 

问： Azure Synapse 为数据安全提供哪些功能？

A. Azure Synapse 提供了多个用于保护数据的解决方案，如 TDE 和审核。 有关详细信息，请参阅[安全性]。

问： 在哪里可以找到 Azure Synapse 符合哪些法律或业务标准？

A. 请访问 [Microsoft 符合性]页面，查明产品（如 SOC 和 ISO）的各种符合性规定。 首先选择 "符合性标题"，然后在页面右侧的 "Microsoft 范围内云服务" 部分中展开 "Azure"，以了解哪些服务符合 Azure Synapse。

问： 能否连接 Power BI？

A. 可以！ 尽管 Power BI 支持直接查询 Azure Synapse，但并不适合大量用户或实时数据。 若要进一步优化 Power BI 性能，请考虑在 Azure Analysis Services 或 Analysis Service IaaS 顶层使用 Power BI。

问： 什么是 SQL Analytics 容量限制？

A. 请参阅当前[容量限制]页。 

问： 为什么缩放/暂停/恢复会花费很长时间？

A. 多种因素可能会影响计算管理操作的时间。 一个常见的长时运行操作的例子是事务回退。 缩放或暂停操作启动时，会阻止所有传入会话和查询。 为了使系统处于稳定状态，必须在操作开始前回退事务。 事务数量越多，其日志大小越大，使系统恢复到稳定状态的操作耗时越久。

## <a name="user-support"></a>用户支持

问： 在何处提交功能请求？

A. 如果有功能请求，请在 [UserVoice] 页上提交

问： 该如何操作？

A. 有关借助 Azure Synapse 进行开发的帮助，可在我们的[Stack Overflow]页面上提出问题。 

问： 如何提交支持票证？

A. 可通过 Azure 门户提交[支持票证]。

## <a name="sql-languagefeature-support"></a>SQL 语言/功能支持 

问： 支持哪些数据类型？

A. 请参阅[数据类型]。

问： 支持哪些表功能？

A. 支持许多功能，这些功能不受支持，并且记录在[不受支持的表功能]中。

## <a name="tooling-and-administration"></a>工具和管理

问： SQL Analytics 是否支持 REST Api？

A. 可以。 Sql Analytics 还提供了可与 SQL 数据库一起使用的大多数 REST 功能。 可以在 REST 文档页或 [MSDN] 中找到 API 信息。


## <a name="loading"></a>正在加载

问： 支持哪些客户端驱动程序？

A. 可在[连接字符串]页找到 DW 驱动程序支持

问： PolyBase 支持哪些文件格式？

答：Orc、RC、Parquet 和平面分隔文本

问：可以使用 PolyBase 连接到哪些数据源？ 

答：[Azure Data Lake Store] 和 [Azure 存储 Blob]

问：连接到 Azure 存储 Blob 或 ADLS 时能否进行计算下推？ 

答：不能，PolyBase 仅与存储组件交互。 

问：能否连接到 HDI？

答：HDI 可使用 ADLS 或 WASB 作为 HDFS 层。 如果将两者中任意一种作为 HDFS 层，可以将该数据加载到 SQL DW。 但是，无法生成 HDI 实例的下推计算。 

## <a name="next-steps"></a>后续步骤
有关 Azure Synapse 的详细信息，请参阅[概述]页。


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[连接字符串]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[支持票证]: ./sql-data-warehouse-get-started-create-support-ticket.md
[安全性]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft 符合性]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[容量限制]: ./sql-data-warehouse-service-capacity-limits.md
[数据类型]: ./sql-data-warehouse-tables-data-types.md
[不受支持的表功能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure 存储 Blob]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Database projects feature request]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[概述]: ./sql-data-warehouse-overview-faq.md
