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
ms.openlocfilehash: f7cac849caf478850707eb0a71b2369dcfa49520
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197175"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics （以前称为 SQL DW）常见问题

## <a name="general"></a>常规

Q. 什么是 Azure Synapse？

A. Azure Synapse 是一个无限制的分析服务，它将数据仓库和大数据分析集中在一起。 它可让你随意使用无服务器点播或预配的资源按比例查询数据。 Azure Synapse 将这两个世界结合在一起，可引入、准备、管理和提供数据以满足即时的 BI 和机器学习需求。 有关详细信息，请参阅[什么是 Azure Synapse 分析](sql-data-warehouse-overview-what-is.md)。

Q. Azure SQL 数据仓库发生了什么情况？

A. Azure Synapse 是 Azure SQL 数据仓库（SQL DW）的发展。 我们采用了相同的行业领先的数据仓库，以获得全新的性能和功能水平。 你可以继续在生产中通过 Azure Synapse 运行现有数据仓库工作负荷，并自动受益于新功能，这些功能以预览版提供。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

Q. SQL Analytics 是什么？

A. SQL Analytics 是 Azure Synapse 中正式发布的企业数据仓库功能。 有关详细信息，请参阅[什么是 Azure Synapse 分析](sql-data-warehouse-overview-what-is.md)。

Q. 如何实现 Azure Synapse 入门？

A. 若要[了解详细信息](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)，可以开始使用[Azure 免费帐户](https://azure.microsoft.com/free/sql-data-warehouse/)或联系销售额。 

Q. Azure Synapse 为数据安全提供哪些功能？

A. Azure Synapse 提供了多个用于保护数据的解决方案，如 TDE 和审核。 有关详细信息，请参阅[安全性](sql-data-warehouse-overview-manage-security.md)。

Q. 在哪里可以找到 Azure Synapse 符合哪些法律或业务标准？

A. 请访问 [Microsoft 符合性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)页面，查明产品（如 SOC 和 ISO）的各种符合性规定。 首先，选择 "符合性标题"。 然后在页面右侧的 "Microsoft 范围内云服务" 部分中展开 "Azure"，以了解哪些服务符合 Azure Synapse。

Q. 能否连接 Power BI？

A. 能！ 尽管 Power BI 支持直接查询 Azure Synapse，但并不适合大量用户或实时数据。 若要进一步优化 Power BI 性能，请考虑在 Azure Analysis Services 或 Analysis Service IaaS 顶层使用 Power BI。

Q. 什么是 SQL Analytics 容量限制？

A. 请参阅当前[容量限制](sql-data-warehouse-service-capacity-limits.md)页。 

Q. 为什么缩放/暂停/恢复会花费很长时间？

A. 几个因素可能会影响计算管理操作的时间。 长时间运行的操作的常见情况是事务回滚。 缩放或暂停操作启动时，会阻止所有传入会话和查询。 为了使系统处于稳定状态，必须在操作开始前回退事务。 事务数量越多，其日志大小越大，使系统恢复到稳定状态的操作耗时越久。

## <a name="user-support"></a>用户支持

Q. 在何处提交功能请求？

A. 如果有功能请求，请在 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 页上提交

Q. 该如何操作？

A. 有关借助 Azure Synapse 进行开发的帮助，可以在我们的[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)页面上提出问题。 

Q. 如何提交支持票证？

A. 可通过 Azure 门户提交[支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="sql-languagefeature-support"></a>SQL 语言/功能支持 

Q. 支持哪些数据类型？

A. 请参阅[数据类型](sql-data-warehouse-tables-data-types.md)。

Q. 支持哪些表功能？

A. 支持许多功能。 不受支持的功能可以在[不支持的表功能](sql-data-warehouse-tables-data-types.md)中找到。

## <a name="tooling-and-administration"></a>工具和管理

Q. SQL Analytics 是否支持 REST Api？

A. 是的。 Sql Analytics 还提供了可与 SQL 数据库一起使用的大多数 REST 功能。 可以在 REST 文档页或 [MSDN](https://msdn.microsoft.com/library/azure/mt163685.aspx) 中找到 API 信息。


## <a name="loading"></a>加载

Q. 支持哪些客户端驱动程序？

A. 可以在 "[连接字符串](sql-data-warehouse-connection-strings.md)" 页上找到对 SQL Analytics 的驱动程序支持

问： PolyBase 支持哪些文件格式？

答：Orc、RC、Parquet 和平面分隔文本

问：可以使用 PolyBase 连接到哪些数据源？ 

答： [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md)和[Azure 存储 blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

问：连接到 Azure 存储 Blob 或 ADLS 时能否进行计算下推？ 

答：不能，PolyBase 仅与存储组件交互。 

问：能否连接到 HDI？

答：HDI 可使用 ADLS 或 WASB 作为 HDFS 层。 如果你有一个作为 HDFS 层，可以将该数据加载到 SQL Analytics 数据仓库中。 但是，无法生成 HDI 实例的下推计算。 

## <a name="next-steps"></a>后续步骤
有关 Azure Synapse 的详细信息，请参阅[概述](sql-data-warehouse-overview-faq.md)页。
