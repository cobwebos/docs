---
title: Azure Synapse Analytics 中 SQL 点播（预览版）的最佳实践
description: 使用 SQL 点播（预览版）时应了解的建议和最佳实践。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429065"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中 SQL 点播（预览版）的最佳实践

本文介绍如何使用 SQL 点播（预览版）的最佳实践集合。 SQL 点播是 Azure Synapse 分析中的额外资源。

## <a name="general-considerations"></a>一般注意事项

SQL 点播允许查询 Azure 存储帐户中的文件。 它没有本地存储或引入功能。 因此，该查询针对的所有文件都是 SQL 点播的外部文件。 与从存储读取文件相关的所有内容都可能会影响查询性能。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>归置 Azure 存储帐户和 SQL 点播

若要最大程度地减少延迟，请将 Azure 存储帐户和 SQL 点播终结点归置。 创建工作区期间预配的存储帐户和终结点位于同一区域。

为了获得最佳性能，如果使用 SQL 点播访问其他存储帐户，请确保它们位于同一区域。 如果它们不在同一区域，则远程和终结点的区域之间的数据网络传输延迟将会增加。

## <a name="azure-storage-throttling"></a>Azure 存储限制

多个应用程序和服务可以访问你的存储帐户。 当应用程序、服务和 SQL 按需工作负荷生成的组合 IOPS 或吞吐量超过存储帐户的限制时，将发生存储限制。 因此，你会对查询性能产生严重的负面影响。

一旦检测到限制，SQL 点播就会对此方案进行内置处理。 SQL 点播将以较慢的速度向存储请求，直到限制得以解决。

> [!TIP]
> 为实现最佳查询执行，在执行查询期间，不应将存储帐户与其他工作负荷进行压力。

## <a name="prepare-files-for-querying"></a>准备文件以进行查询

如果可能，可以准备文件以获得更好的性能：

- 将 CSV 转换为 Parquet-Parquet 的格式为柱状。 由于它已经过压缩，因此，其文件大小要小于具有相同数据的 CSV 文件。 SQL 点播需要更少的时间和存储请求来读取它。
- 如果查询以单个大文件为目标，则将其拆分为多个较小的文件将会带来好处。
- 请尝试将 CSV 文件的大小保持低于 10 GB。
- 最好为单个 OPENROWSET 路径或外部表位置设置大小相同的文件。
- 通过将分区存储到不同文件夹或文件名来对数据进行分区，请检查[使用 filename 和 filepath 函数以针对特定分区](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用 fileinfo 和 filepath 函数针对特定分区

数据通常在分区中进行组织。 可以指示 SQL 按需查询特定文件夹和文件。 此函数将减少查询读取和处理所需的文件数和数据量。 增加的优点是您将获得更好的性能。

有关详细信息，请查看[filename](develop-storage-files-overview.md#filename-function)和[filepath](develop-storage-files-overview.md#filepath-function)函数以及如何[查询特定文件](query-specific-files.md)的示例。

如果存储的数据未分区，请考虑将其分区，以便可以使用这些函数来优化针对这些文件的查询。 从 SQL 按需[查询分区 Spark 表](develop-storage-files-spark-tables.md)时，查询将自动仅针对所需的文件。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 增强查询性能和联接

[CETAS](develop-tables-cetas.md)是 SQL 点播中最重要的功能之一。 CETAS 是一种并行操作，用于创建外部表元数据并将选择查询结果导出到你的存储帐户中的一组文件。

您可以使用 CETAS 将经常使用的查询部分（如联接的引用表）存储到一组新的文件。 接下来，可以联接到此单个外部表，而不是重复多个查询中的常用联接。

当 CETAS 生成 Parquet 文件时，当第一个查询针对此外部表时，将自动创建统计信息，从而提高性能。

## <a name="next-steps"></a>后续步骤

有关常见问题和解决方法，请参阅[故障排除](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)一文。 如果你使用的是 sql 池，而不是按需使用 SQL，请参阅[sql 池的最佳实践](best-practices-sql-pool.md)一文，了解具体指导。
