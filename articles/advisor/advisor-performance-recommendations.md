---
title: Azure 顾问性能建议 | Microsoft Docs
description: 使用顾问优化 Azure 部署的性能。
services: advisor
documentationcenter: NA
author: kasparks
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: 963960657fb8c16307dbf062c0b16cd74a4a7b3f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101711"
---
# <a name="advisor-performance-recommendations"></a>顾问性能建议

Azure 顾问性能建议有助于提高关键业务应用程序的速度和响应能力。 可通过顾问从顾问仪表板的“性能”选项卡获取性能建议。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>减少流量管理器配置文件上的 DNS 生存时间，可更快地故障转移到正常运行的终结点

使用流量管理器配置文件上的[生存时间 (TTL) 设置](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可以指定在给定终结点停止响应查询时切换终结点的速度。 减少 TTL 值意味着客户端将更快地路由到正常运行的终结点。

Azure 顾问将识别配置了较长 TTL 的流量管理器配置文件，并建议将 TTL 配置为 20 秒或 60 秒，具体取决于配置文件是否配置为使用[快速故障转移](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)。

## <a name="improve-database-performance-with-sql-db-advisor"></a>通过 SQL DB 顾问提高数据库性能

顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，提供建议以改进 SQL Azure 数据库性能。 SQL 数据库顾问通过分析使用情况历史记录来评估 SQL Azure 数据库的性能。 提供的建议最适合运行数据库典型工作负荷。 

> [!NOTE]
> 若要获取建议，数据库必须具有一周左右的使用量，且该周内必须有一些一致的活动。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关 SQL 数据库顾问的详细信息，请参阅 [SQL 数据库顾问](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="improve-app-service-performance-and-reliability"></a>提高应用服务性能和可靠性

Azure 顾问集成最佳做法建议，可改善应用服务体验，并发现相关平台的功能。 以下为应用服务建议示例：
* 检测其中应用运行时已耗尽内存或 CPU 资源的实例，并提出缓解措施。
* 检测在其中并置资源（如 Web 应用和数据库）的实例，以改善性能并降低成本。 

有关应用服务建议的详细信息，请参阅 [Best Practices for Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)（Azure 应用服务的最佳实践）。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用托管磁盘防止磁盘 I/O 限制

顾问将识别属于即将达到其可伸缩性目标的存储帐户的虚拟机。 这使得它容易遭受 I/O 限制。 顾问会建议这些虚拟机使用托管磁盘以防止性能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>可以通过使用高级磁盘来提高虚拟机磁盘的性能和可靠性

顾问将识别其中的标准磁盘在存储帐户上具有大量事务的虚拟机，并建议升级到高级磁盘。 

Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储帐户存储数据的虚拟机磁盘。 为使应用程序实现最佳性能，建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>删除 SQL 数据仓库表的数据倾斜以提高查询性能

运行工作负荷时，数据倾斜可能会造成不必要的数据移动或资源瓶颈。 顾问将检测大于 15% 的分布数据倾斜，并建议重新分布数据和重新访问表分布键选择。 若要了解有关识别和删除倾斜的详细信息，请参阅[倾斜疑难解答](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>创建或更新 SQL 数据仓库表的过期表统计信息以提高查询性能

顾问标识不包含最新[表统计信息](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的表并建议创建或更新表统计信息。 SQL 数据仓库查询优化器使用最新统计信息来估计基数或查询结果中的行数，这样一来，查询优化器能够创建高质量的查询计划，以提高性能。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>通过纵向扩展来优化 SQL 数据仓库上的缓存利用率，从而提升查询性能

Azure 顾问会检测 SQL 数据仓库是否具有高缓存使用百分比和低命中百分比。 这表示存在高缓存逐出，可能会影响 SQL 数据仓库的性能。 顾问建议你对 SQL 数据仓库进行纵向扩展以确保为工作负荷分配足够的缓存容量。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>将 SQL 数据仓库表转换为复制表以提高查询性能

顾问会识别非复制但是会受益于转换的表，并建议转换这些表。 建议是基于 SQL 数据仓库表的复制表大小、列数、表分布类型以及分区数提出的。 还可能会根据上下文在建议中提供额外的启发。 若要详细了解此建议是如何确定的，请参阅 [SQL 数据仓库建议](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>将存储帐户迁移到 Azure 资源管理器来获取所有最新 Azure 功能。

将存储帐户部署模型迁移到 Azure 资源管理器 (ARM) 可利用模板部署和更多的安全选项，并可升级到 GPv2 帐户来利用 Azure 存储的最新功能。 顾问将识别使用经典部署模型的任何独立存储帐户，并建议迁移到 ARM 部署模型。 

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“性能”选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)

