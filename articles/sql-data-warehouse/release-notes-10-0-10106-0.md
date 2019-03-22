---
title: Azure SQL 数据仓库发行说明 | Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 0b0d302dcd86f18eef6bf7600dd93878d0d9bd99
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902682"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 数据仓库发行说明
本文汇总了 [Azure SQL 数据仓库](sql-data-warehouse-overview-what-is.md)的最新版本中的新功能和改进。 本文还列出了不与此版本直接相关但在同一时间范围内发布的值得注意的内容更新。 有关对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL 数据仓库版本 10.0.10106.0（一月）

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**返回结果时的排序依据优化**|在此版本中，SELECT…ORDER BY 的性能已得到大幅提升。   现在，所有计算节点都将其结果发送到单个计算节点，该节点对结果进行合并和排序，然后，这些结果通过该计算节点返回给用户。  当查询结果集包含大量的行时，通过单个计算节点进行合并可以显著提升性能。 以前，查询执行引擎按每个计算节点将结果排序，再将结果流式传输到控制节点，然后合并结果。|
|**PartitionMove 和 BroadcastMove 的数据移动增强**|在 Azure SQL 数据仓库 Gen2 中，ShuffleMove 类型的数据移动步骤使用[性能增强博客文章](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)中所述的即时数据移动技术。 在此版本中，PartitionMove 和 BroadcastMove 类型的数据移动步骤以相同的即时数据移动技术为后盾。 利用这些类型的数据移动步骤的用户查询可以提升运行性能。 无需更改代码便可利用这些性能改进。|
|**值得注意的 Bug**|Azure SQL 数据仓库不正确版本 -“SELECT @@VERSION”可能返回不正确的版本 10.0.9999.0。 当前版本的正确版本是 10.0.10106.0。 此 bug 已报告，正在评审。

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|无 | |
| | |

## <a name="next-steps"></a>后续步骤
- [创建 SQL 数据仓库](./create-data-warehouse-portal.md)

## <a name="more-information"></a>详细信息
- [博客 - Azure SQL 数据仓库](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客户顾问团队博客](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客户成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stackoverflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 词汇表](../azure-glossary-cloud-terminology.md)
