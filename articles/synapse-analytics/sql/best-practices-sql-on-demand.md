---
title: Azure 突触分析中 SQL 按需（预览）的最佳做法
description: 在使用 SQL 按需（预览）时，应了解的建议和最佳实践。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429065"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure 突触分析中 SQL 按需（预览）的最佳做法

在本文中，您将找到一系列使用 SQL 按需（预览）的最佳做法。 SQL 按需是 Azure 同步分析中的附加资源。

## <a name="general-considerations"></a>一般注意事项

SQL 按需允许您查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能。 因此，查询目标的所有文件都是 SQL 按需的外部文件。 与从存储读取文件相关的一切都可能会影响查询性能。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>按需为 Azure 存储帐户和 SQL 提供共同定位

为了最大程度地减少延迟，请共同定位 Azure 存储帐户和 SQL 按需终结点。 工作区创建期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果使用 SQL 按需访问其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域中，则数据在远程区域和终结点区域之间的网络传输的延迟会增加。

## <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用程序和服务可以访问您的存储帐户。 当应用程序、服务和 SQL 按需工作负载生成的合并 IOPS 或吞吐量超过存储帐户的限制时，就会发生存储限制。 因此，您将遇到对查询性能的重大负面影响。

检测到限制后，SQL 按需已内置处理此方案。 SQL 按需将以较慢的速度发出存储请求，直到解决限制问题。

> [!TIP]
> 为了获得最佳的查询执行，在查询执行期间，不应将存储帐户与其他工作负载重压。

## <a name="prepare-files-for-querying"></a>准备用于查询的文件

如果可能，您可以准备文件以获得最佳性能：

- 将 CSV 转换为镶木地板 - 镶木地板为柱形格式。 由于已压缩，其文件大小小于具有相同数据的 CSV 文件。 SQL 按需读取它所需的时间和存储请求将更少。
- 如果查询以单个大型文件为目标，则可以将其拆分为多个较小的文件。
- 请尝试将 CSV 文件大小保持在 10 GB 以下。
- 最好为单个 OPENROWSET 路径或外部表位置提供大小相等的文件。
- 通过将分区存储到不同的文件夹或文件名来划分数据 - 检查[使用文件名和文件路径函数来定位特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用文件信息和文件路径函数定位特定分区

数据通常以分区方式组织。 您可以指示 SQL 按需查询特定文件夹和文件。 此函数将减少查询需要读取和处理的文件数量和数据量。 额外的好处是，你会取得更好的表现。

有关详细信息，请查看[文件名](develop-storage-files-overview.md#filename-function)和[文件路径](develop-storage-files-overview.md#filepath-function)函数以及如何[查询特定文件](query-specific-files.md)的示例。

如果存储的数据未分区，请考虑对其进行分区，以便可以使用这些函数优化针对这些文件的查询。 当查询从 SQL 按需[分区 Spark 表](develop-storage-files-spark-tables.md)时，查询将自动仅定位所需的文件。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 提高查询性能和联接

[CETAS](develop-tables-cetas.md)是 SQL 按需提供的最重要功能之一。 CETAS 是一个并行操作，用于创建外部表元数据并将 SELECT 查询结果导出到存储帐户中的一组文件。

可以使用 CETAS 将常用的查询部分（如联接的引用表）存储到一组新的文件。 接下来，您可以加入此外部表，而不是在多个查询中重复公共联接。

当 CETAS 生成 Parquet 文件时，当第一个查询针对此外部表时，将自动创建统计信息，从而提高性能。

## <a name="next-steps"></a>后续步骤

查看[疑难解答](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)文章，查看常见问题和解决方案。 如果您正在使用 SQL 池而不是按需使用 SQL，请参阅 SQL[池的最佳实践](best-practices-sql-pool.md)一文，了解特定指南。
