---
title: 性能最佳做法和配置指南 - Azure SQL Edge
description: 了解 Azure SQL Edge 中的性能最佳做法和配置指南
keywords: SQL Edge, 数据保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 35985404d5ac97940c324c3ad7f7d46c959b4902
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934803"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>性能最佳做法和配置指南

Azure SQL Edge 提供了几种可用于提高 SQL Edge 部署性能的特性和功能。 本文提供了一些最佳做法和建议，以最大程度地提高性能。 

## <a name="best-practices"></a>最佳做法 

### <a name="configure-multiple-tempdb-data-files"></a>配置多个 tempdb 数据文件

默认情况下，Azure SQL Edge 在容器初始化过程中仅创建一个 tempdb 数据文件。 建议在部署后考虑创建多个 tempdb 数据文件。 有关详细信息，请参阅[建议以减少 SQL Server tempdb 数据库中的分配争用](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d)一文。

### <a name="use-clustered-columnstore-indexes-where-possible"></a>尽可能使用聚集列存储索引

IoT 和 Edge 设备往往会生成大量数据，这些数据通常在某个时间范围内汇总，以便进行分析。 无论进行哪种分析，都很少使用单个数据行。 列存储索引非常适合存储和查询此类大型数据集。 此索引使用基于列的数据存储和查询处理，与面向行的传统存储相比，最多可实现 10 倍的查询性能提升。 此外，与处理非压缩数据相比，处理压缩数据可将性能提升 10 倍。 有关详细信息，请参阅[列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview)

此外，其他 Azure SQL Edge 功能（如数据流式处理和数据保留）将受益于针对数据插入和数据删除进行的列存储优化。 

### <a name="simple-recovery-model"></a>简单恢复模式

由于边缘设备上的存储可能受限，因此默认情况下，Azure SQL Edge 中的所有用户数据库都使用简单恢复模式。 简单恢复模式会自动回收日志空间以减少空间需求，实际上不再需要管理事务日志空间。 在可用存储有限的边缘设备上，这可能很有用。 有关简单恢复模式和其他可用恢复模式的详细信息，请参阅[恢复模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)

简单恢复模式不支持需要事务日志备份的日志传送和时间点还原等操作。  

## <a name="advanced-configuration"></a>高级配置 

### <a name="setting-memory-limits"></a>设置内存限制

对于缓冲池，Azure SQL Edge 最多支持 64 GB 内存，而 SQL Edge 容器中运行的附属进程可能需要更多内存。 在内存有限的小型边缘设备上，建议限制 SQL Edge 容器可用的内存，以便 Docker 主机和其他边缘进程或模块可以正常运行。 可以使用以下机制控制 SQL Edge 可用的总内存。 

- 限制 SQL Edge 容器可用的内存：可以通过使用容器运行时配置选项 `--memory` 来限制 SQ​​L Edge 容器可用的总内存。 有关限制 SQL Edge 容器可用内存的详细信息，请参阅[内存、CPU 和 GPU 的运行时选项](https://docs.docker.com/config/containers/resource_constraints/)。

- 限制容器内 SQL 进程可用的内存：默认情况下，容器内的 SQL 进程仅使用 80% 的可用物理 RAM。 对于大多数部署，可以使用默认配置。 但在某些情况下，数据流式处理和 SQL Edge 容器中运行的 ONNX 进程可能需要额外的内存。 在这种情况下，可以使用 mssql-conf 文件中的 `memory.memorylimitmb` 设置来控制 SQL 进程可用的内存。 有关详细信息，请参阅[使用 mssql.conf 文件进行配置](configure.md#configure-by-using-an-mssqlconf-file)。

设置内存限制时，注意不要将此值设置得太低。 如果没有为 SQL 进程设置足够的内存，可能会严重影响 SQL 性能。

### <a name="delayed-durability"></a>延迟持续性

Azure SQL Edge 中的事务可以是完全持久事务、SQL Server 默认事务或延迟持久事务（也称作迟缓提交）。

完全持久事务提交是同步的，仅在事务的日志记录写入磁盘后报告提交成功，并将控制权归还客户端。 延迟持久事务提交是异步的，并在事务的日志记录写入磁盘之前报告提交成功。 事务要成为持久事务，必须将事务日志条目写入磁盘。 当事务日志条目刷新到磁盘时，延迟持久事务成为持久事务。 

在可以容忍部分数据丢失的部署中或在存储速度较慢的边缘设备上，可以使用延迟持久性来优化数据引入和基于数据保留的清理。 有关详细信息，请参阅[控制事务持续性](https://docs.microsoft.com/sql/relational-databases/logs/control-transaction-durability)。


### <a name="linux-os-configurations"></a>Linux OS 配置 

请考虑使用以下 [Linux 操作系统配置](https://docs.microsoft.com/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration)设置，以体验 SQL 安装的最佳性能。







