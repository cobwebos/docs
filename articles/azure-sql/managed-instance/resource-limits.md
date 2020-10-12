---
title: 资源限制
titleSuffix: Azure SQL Managed Instance
description: 本文概述了 Azure SQL 托管实例的资源限制。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: 71392b652f305f085e8eddbfe75e0585a756bc4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618108"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL 托管实例资源限制概述
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文概述了 Azure SQL 托管实例的技术特征和资源限制，并提供了有关如何请求提高这些限制的信息。

> [!NOTE]
> 有关支持的功能和 T-SQL 语句的差异，请参阅[功能差异](../database/features-comparison.md)和 [T-SQL 语句支持](transact-sql-tsql-differences-sql-server.md)。 有关 Azure SQL 数据库和 SQL 托管实例中服务层级之间的一般差异，请参阅[服务层级比较](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)。

## <a name="hardware-generation-characteristics"></a>硬件代次特征

SQL 托管实例的某些特征和资源限制取决于底层基础结构和体系结构。 SQL 托管实例可部署在两个硬件代系上：Gen4 和 Gen5。 硬件代次具有不同的特征，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **硬件** | Intel® E5-2673 v3 (Haswell) 2.4 GHz 处理器，附加 SSD vCore = 1 PP (物理内核)  | Intel® E5-2673 v4 (Broadwell) 2.3 GHz、Intel® SP-8160 (Skylake) 和 Intel® 8272CL 2.5 () GHz 处理器，fast NVMe SSD，vCore = 1 LP (超线程)  |
| **vCore 数目** | 8、16、24 个 vCore | 4、8、16、24、32、40、64、80 个 vCore |
| **最大内存（内存/核心比）** | 每个 vCore 7 GB<br/>添加更多 vCore 以获得更多内存。 | 每个 vCore 5.1 GB<br/>添加更多 vCore 以获得更多内存。 |
| **最大内存中 OLTP 存储** | 实例限制：每个 vCore 1 - 1.5 GB| 实例限制：每个 vCore 0.8 - 1.65 GB |
| **最大实例保留存储** |  常规用途：8 TB<br/>业务关键：1 TB | 常规用途：8 TB<br/> 业务关键型 1 TB、2 TB 或 4 TB，具体取决于核心数 |

> [!IMPORTANT]
> - Gen4 硬件正在逐步淘汰，不能再用于新部署。 SQL 托管实例的所有新实例都必须部署在 Gen5 硬件上。
> - 请考虑[将 SQL 托管实例的实例迁移到 Gen 5](../database/service-tiers-vcore.md) 硬件，体验范围更广泛的 vCore 和存储可伸缩性、加速网络、最佳 IO 性能和最小延迟。

### <a name="in-memory-oltp-available-space"></a>内存中 OLTP 可用空间 

[业务关键](../database/service-tier-business-critical.md)服务层级中的内存中 OLTP 空间量取决于 vCore 数和硬件代次。 下表列出了可用于内存中 OLTP 对象的内存限制。

| 内存中 OLTP 空间  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 个 vCore  | 3.14 GB | |   
| 8 个 vCore  | 6.28 GB | 8 GB |
| 16 个 vCore | 15.77 GB | 20 GB |
| 24 个 vCore | 25.25 GB | 36 GB |
| 32 个 vCore | 37.94 GB | |
| 40 个 vCore | 52.23 GB | |
| 64 个 vCore | 99.9 GB    | |
| 80 个 vCore | 131.68 GB| |

## <a name="service-tier-characteristics"></a>服务层特征

SQL 托管实例有两个服务层级：[常规用途](../database/service-tier-general-purpose.md)和[业务关键](../database/service-tier-business-critical.md)。 这些层级提供[不同的功能](../database/service-tiers-general-purpose-business-critical.md)，如下表中所述。

> [!Important]
> 业务关键服务层提供了 SQL 托管实例的附加内置副本（次要副本），可用于只读工作负载。 如果可以隔离读写查询和只读/分析/报告查询，则能够以相同的价格获得两倍的 vCore 和内存。 次要副本可能滞后于主要实例几秒，因此它旨在卸载不需要确切的当前数据状态的报告/分析工作负荷。 在下表中，**只读查询**是在次要副本上执行的查询。

| **功能** | **常规用途** | **业务关键** |
| --- | --- | --- |
| vCore 数目\* | Gen4：8、16、24<br/>Gen5：4、8、16、24、32、40、64、80 | Gen4：8、16、24 <br/> Gen5：4、8、16、24、32、40、64、80 <br/>\*相同数量的 vCore 专用于只读查询。 |
| 最大内存 | Gen4：56 GB - 168 GB (7GB/vCore)<br/>Gen5：20.4 GB - 408 GB (5.1GB/vCore)<br/>添加更多 vCore 以获得更多内存。 | Gen4：56 GB - 168 GB (7GB/vCore)<br/>Gen5： 20.4 GB-408 GB (5.1 GB/vCore) 用于读写查询<br/>为只读查询额外提供 20.4 GB - 408 GB (5.1GB/vCore)。<br/>添加更多 vCore 以获得更多内存。 |
| 最大实例存储大小（预留） | - 4 个 vCore 2 TB（仅限 Gen5）<br/>- 8 TB，适用于其他大小 | Gen4：1 TB <br/> Gen5： <br/>- 1 TB，适用于 4、8、16 个 vCore<br/>- 2 TB（适用于 24 个 vCore）<br/>- 32、40、64、80 个 vCore 4 TB |
| 最大数据库大小 | 不超过当前可用的实例大小（最大为 2 TB - 8 TB，具体取决于 vCore 数）。 | 不超过当前可用的实例大小（最大为 1 TB - 4 TB，具体取决于 vCore 数）。 |
| 最大 tempDB 大小 | 限制为 24 GB/vCore (96 - 1,920 GB) 和当前可用的实例存储大小。<br/>添加更多 Vcore 以获得更多 TempDB 空间。<br/> 日志文件大小限制为 120 GB。| 不超过当前可用的实例存储大小。 |
| 每个实例的数据库数目上限 | 除非已达到实例存储大小限制，否则为 100。 | 除非已达到实例存储大小限制，否则为 100。 |
| 每个实例的数据库文件数上限 | 除非已达到实例存储大小或 [Azure 高级磁盘存储分配空间](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)限制，否则最大为 280。 | 除非已达到实例存储大小限制，否则为每个数据库 32,767 个文件。 |
| 最大数据文件大小 | 限制为当前可用的实例存储大小（最大 2 TB - 8 TB）和 [Azure 高级磁盘存储分配空间](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)。 | 限制为当前可用的实例存储大小（最大为 1 TB - 4 TB）。 |
| 最大日志文件大小 | 限制为 2 TB 和当前可用的实例存储大小。 | 限制为 2 TB 和当前可用的实例存储大小。 |
| 数据/日志 IOPS（近似值） | 每个实例最多 30-40 K IOPS*，每个文件 500 - 7500<br/>\*[增加文件大小以获得更多 IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (4000 IOPS/vCore) <br/>添加更多 Vcore 以获得更好的 IO 性能。 |
| 日志写入吞吐量限制（每个实例） | 3 MB/s（每个 vCore）<br/>每个实例最大 120 MB/秒<br/>每个数据库 22-65 MB/秒<br/>\*[增加文件大小以获得更好的 IO 性能](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s（每个 vCore）<br/>最大 96 MB/秒 |
| 数据吞吐量（近似值） | 100 - 250 MB/s（每个文件）<br/>\*[增加文件大小以获得更好的 IO 性能](#file-io-characteristics-in-general-purpose-tier) | 无限制。 |
| 存储 IO 延迟（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 内存中 OLTP | 不支持 | [可用大小取决于 vCore 数](#in-memory-oltp-available-space) |
| 最大会话数 | 30000 | 30000 |
| 最大并发工作线程数（请求数） | Gen4：210 * vCore 数目 + 800<br>Gen5：105 * vCore 数目 + 800 | Gen4：210 * vCore 计数 + 800<br>Gen5：105 * vCore 计数 + 800 |
| [只读副本](../database/read-scale-out.md) | 0 | 1（包括在价格中） |
| 计算隔离 | Gen5：<br/>-支持 80 个 vCore<br/>-不支持其他大小<br/><br/>由于弃用，不支持 Gen4|Gen5：<br/>-支持60、64、80 Vcore<br/>-不支持其他大小<br/><br/>由于弃用，不支持 Gen4|


一些其他注意事项： 

- **当前可用实例存储大小**是预留实例大小与已用存储空间之差。
- 与最大存储大小限制进行比较的实例存储大小同时包括用户数据库和系统数据库中的数据和日志文件大小。 可以使用 [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) 系统视图来确定数据库使用的空间总量。 错误日志不会持久保存，不包括在大小中。 备份不包括在存储大小中。
- 常规用途层上的吞吐量和 IOPS 还要取决于未显式受到 SQL 托管实例限制的[文件大小](#file-io-characteristics-in-general-purpose-tier)。
  可以使用[自动故障转移组](../database/auto-failover-group-configure.md)在不同的 Azure 区域中创建另一个可读副本
- 最大实例 IOPS 取决于工作负荷的文件布局和分布。 例如，如果创建 7 个 1 TB 文件（每个文件最大 5K IOPS）和 7 个小文件（小于 128 GB，每个文件 500 IOPS），并且工作负载可以使用所有文件，则每个实例可以处理 38500 IOPS (7x5000+7x500)。 请注意，某些 IOPS 还将用于自动备份。

有关详细信息，请查看 [本文中 SQL 托管实例池的资源限制](instance-pools-overview.md#resource-limitations)。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>“常规用途”层级中的文件 IO 特征

在常规用途服务层中，每个数据库文件都将获得专用 IOPS 和吞吐量，具体取决于文件大小。 较大的文件可以获得更多的 IOPS 和吞吐量。 下表显示了数据库文件的 IO 特征：

| 文件大小 | >=0 且 <=128 GiB | >128 和 <= 512 GiB | >0.5 且 <=1 TiB    | >1 且 <=2 TiB    | >2 且 <=4 TiB | >4 且 <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 每个文件的 IOPS       | 500   | 2300              | 5000              | 7500              | 7500              | 12,500   |
| 每个文件的吞吐量 | 100 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/秒 | 

如果注意到某个数据库文件的 IO 延迟较高，或者发现 IOPS/吞吐量即将达到限制，可以通过[增大文件大小](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)来提高性能。

最大日志写入吞吐量也存在实例级别的限制（即 22 MB/秒），因此，你可能无法达到日志文件的最大文件吞吐量，因为你即将达到实例吞吐量限制。

## <a name="supported-regions"></a>支持的区域

SQL 托管实例只能在[支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中创建。 若要在当前不受支持的区域中创建 SQL 托管实例，可以 [通过 Azure 门户发送支持请求](../database/quota-increase-request.md)。

## <a name="supported-subscription-types"></a>支持的订阅类型

SQL 托管实例当前仅支持以下类型的订阅上的部署：

- [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [云服务提供商 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 订阅者每月 Azure 信用额度的订阅](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>区域资源限制

> [!Note]
> 有关订阅的区域可用性的最新信息，请首先选中 " [选择区域](https://aka.ms/sqlcapacity)"。

支持的订阅类型可以包含每个区域的有限数量的资源。 对于每个 Azure 区域，SQL 托管实例有两个默认的限制 (根据订阅类型的类型，可以通过在 Azure 门户中创建特殊的 [支持请求](../database/quota-increase-request.md) 来提高按需增加的数量：

- **子网限制**：在单个区域中部署 SQL 托管实例的实例的子网数目上限。
- **vCore 单元限制**：可跨单一区域的所有实例部署的 vCore 单元数上限。 一个 GP vCore 使用一个 vCore 单元，一个 BC vCore 采用 4 个 vCore 单位。 实例总数不受限制，只要在 vCore 单元限制内即可。

> [!Note]
> 这些限制是默认设置，不是技术限制。 如果在当前区域中需要更多实例，则可以通过在 [Azure 门户中创建特殊支持请求](../database/quota-increase-request.md) 来提高限制。 或者，可以在另一个 Azure 区域中创建 SQL 托管实例的新实例，而无需发送支持请求。

下表显示了支持的订阅类型的**默认区域限制**（可以使用下面所述的支持请求扩展默认限制）：

|订阅类型| SQL 托管实例子网的最大数目 | vCore 单元数目上限* |
| :---| :--- | :--- |
|即用即付|3|320|
|CSP |8 (15 在某些区域中 * * ) |960 (1440 在某些地区 * * ) |
|即用即付开发/测试|3|320|
|Enterprise 开发/测试|3|320|
|EA|8 (15 在某些区域中 * * ) |960 (1440 在某些地区 * * ) |
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional 和 MSDN 平台|2|32|

\* 在规划部署时，请考虑业务关键 (BC) 服务层需要四 (4) 倍于常规用途 (GP) 服务层的容量。 例如： 1 GP vCore = 1 vCore unit 和 1 BC vCore = 4 vCore 单位。 若要简化对默认限制的消耗分析，请汇总部署了 SQL 托管实例的区域中所有子网的 vCore 单位，并将结果与订阅类型的实例单位限制进行比较。 **VCore 单位限制的最大数量** 适用于区域中的每个订阅。 每个子网没有任何限制，只不过跨多个子网部署的所有 Vcore 的总和必须小于或等于 **vCore 单元的最大数目**。

\*\* 以下区域提供了更大的子网和 vCore 限制：澳大利亚东部、美国东部、美国东部2、北欧、美国中南部、东南亚、英国南部、西欧、美国西部2。

> [!IMPORTANT]
> 如果 vCore 和子网限制为0，则表示未设置订阅类型的默认区域限制。 还可以按照相同的过程提供所需的 vCore 和子网值，使用配额增加请求，以在特定区域中获取订阅访问权限。

## <a name="request-a-quota-increase"></a>请求增加配额

如果您在当前区域需要更多实例，则发送支持请求以使用 Azure 门户扩展配额。 有关详细信息，请参阅 [AZURE SQL 数据库的请求配额增加](../database/quota-increase-request.md)。

## <a name="next-steps"></a>后续步骤

- 有关 SQL 托管实例的详细信息，请参阅[什么是 SQL 托管实例](sql-managed-instance-paas-overview.md)。
- 有关定价信息，请参阅 [SQL 托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要了解如何创建第一个 SQL 托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
