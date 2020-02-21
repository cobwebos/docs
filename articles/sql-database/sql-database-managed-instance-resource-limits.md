---
title: 资源限制-托管实例
description: 本文概述托管实例的 Azure SQL 数据库资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/18/2020
ms.openlocfilehash: 6e6d4ea6c96949a60677bcf3bf40a53ec3a251c7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526852"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>概述 Azure SQL 数据库托管实例资源限制

本文概述了 Azure SQL 数据库托管实例的技术特性和资源限制，并提供了有关如何请求增加这些限制的信息。

> [!NOTE]
> 有关支持的功能和 T-SQL 语句的差异，请参阅[功能差异](sql-database-features.md)和 [T-SQL 语句支持](sql-database-managed-instance-transact-sql-information.md)。 有关单一数据库和托管实例中的服务层之间的常规 differencess，请参阅[服务层比较](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)。

## <a name="hardware-generation-characteristics"></a>硬件代系特性

托管实例具有依赖于底层基础结构和体系结构的特征和资源限制。 可在两个硬件代上部署 Azure SQL 数据库托管实例： Gen4 和 Gen5。 硬件代具有不同的特征，如下表中所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬件 | Intel E5-2673 v3 (Haswell) 2.4-GHz 处理器、附加的 SSD vCore = 1 PP（物理核心） | Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）处理器，fast NVMe SSD，vCore = 1 LP （超线程） |
| vCore 数目 | 8、16、24 个 vCore | 4、8、16、24、32、40、64、80 Vcore |
| 最大内存（内存/核心比） | 每个 vCore 7 GB<br/>添加更多 Vcore 以获取更多内存。 | 每个 vCore 5.1 GB<br/>添加更多 Vcore 以获取更多内存。 |
| 最大内存中 OLTP 内存 | 实例限制： 1-1.5 GB/vCore| 实例限制：每 vCore 0.8-1.65 GB |
| 最大实例保留存储 |  常规用途： 8 TB<br/>业务关键： 1 TB | 常规用途： 8 TB<br/> 业务关键 1 TB、2 TB 或 4 TB，具体取决于核心数 |

> [!IMPORTANT]
> - Gen4 硬件正在被淘汰，不再适用于新部署。 所有新的托管实例都必须部署在 Gen5 硬件上。
> - 考虑将[托管实例移至第5代](sql-database-service-tiers-vcore.md)硬件，以体验更广泛的 vCore 和存储可伸缩性、加速网络、最佳 IO 性能和最小滞后时间。

### <a name="in-memory-oltp-available-space"></a>内存中 OLTP 可用空间 

[业务关键](sql-database-service-tier-business-critical.md)服务层中的内存中 OLTP 空间量取决于 vcore 和硬件生成的数量。 下表列出了可用于内存中 OLTP 对象的内存限制。

| 内存中 OLTP 空间  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 Vcore  | 3.14 GB | |   
| 8 Vcore  | 6.28 GB | 8 GB |
| 16 Vcore | 15.77 GB | 20 GB |
| 24 Vcore | 25.25 GB | 36 GB |
| 32 Vcore | 37.94 GB | |
| 40 Vcore | 52.23 GB | |
| 64 Vcore | 99.9 GB    | |
| 80 Vcore | 131.68 GB| |

## <a name="service-tier-characteristics"></a>服务层特征

托管实例有两个服务层：[常规用途](sql-database-service-tier-general-purpose.md)和[业务关键](sql-database-service-tier-business-critical.md)。 这些层提供了[不同的功能](sql-database-service-tiers-general-purpose-business-critical.md)，如下表中所述。

> [!Important]
> 业务关键服务层提供了可用于只读工作负荷的实例（辅助副本）的附加内置副本。 如果可以分离读写查询和只读/分析/报告查询，则会获得相同价格的两倍 Vcore 和内存。 辅助副本可能会在主实例之后滞后几秒钟，因此它旨在卸载不需要精确数据状态的报表/分析工作负荷。 在下表中，**只读查询**是在辅助副本上执行的查询。

| **功能** | **常规用途** | **业务关键** |
| --- | --- | --- |
| vCore 数目\* | Gen4：8、16、24<br/>Gen5：4，8，16，24，32，40，64，80 | Gen4：8、16、24 <br/> Gen5：4，8，16，24，32，40，64，80 <br/>\*相同数量的 Vcore 专门用于只读查询。 |
| 最大内存 | Gen4： 56 GB-168 GB （7GB/vCore）<br/>Gen5： 20.4 GB-408 GB （5.1 GB/vCore）<br/>添加更多 Vcore 以获取更多内存。 | Gen4： 56 GB-168 GB （7GB/vCore）<br/>Gen5： 20.4 GB-408 GB （5.1 GB/vCore）用于读写查询<br/>+ 其他 20.4 GB-408 GB （5.1 GB/vCore）用于只读查询。<br/>添加更多 Vcore 以获取更多内存。 |
| 最大实例存储大小（保留） | -2 TB （仅限 Gen5）<br/>对于其他大小为 8 TB | Gen4： 1 TB <br/> Gen5： <br/>-1 TB，适用于4、8、16 Vcore<br/>- 24 个 vCore 2 TB<br/>- 32、40、64、80 个 vCore 4 TB |
| 最大数据库大小 | 当前可用实例大小（最大为 2 TB-8 TB，具体取决于 Vcore 数）。 | 最高当前可用实例大小（最大 1 TB-4 TB，取决于 Vcore 数）。 |
| 最大 tempDB 大小 | 限制为 24 GB/vCore （96-1920 GB）和当前可用的实例存储大小。<br/>添加更多 Vcore 以获取更多 TempDB 空间。<br/> 日志文件大小限制为 120 GB。| 最多当前可用的实例存储大小。 |
| 每个实例的数据库数目上限 | 100，除非已达到实例存储大小限制。 | 100，除非已达到实例存储大小限制。 |
| 每个实例的最大数据库文件数 | 最大为280，除非已达到实例存储大小或[Azure 高级磁盘存储空间](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)的限制。 | 每个数据库32767个文件，除非已达到实例存储大小限制。 |
| 数据文件最大大小 | 仅限当前可用的实例存储大小（最大 2 TB-8 TB）和[Azure 高级磁盘存储分配空间](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)。 | 仅限当前可用的实例存储大小（最大为 1 TB-4 TB）。 |
| 最大日志文件大小 | 仅限 2 TB 和当前可用的实例存储大小。 | 仅限 2 TB 和当前可用的实例存储大小。 |
| 数据/日志 IOPS（近似） | 每个实例最多 30-40 K IOPS *，500-每文件7500<br/>\*[增加文件大小以获取更多 IOPS](#file-io-characteristics-in-general-purpose-tier)| 5.5 k-110 K （1375 IOPS/vCore）<br/>添加更多的 Vcore，以获得更好的 IO 性能。 |
| 日志写入吞吐量限制（每个实例） | 3 MB/s（每个 vCore）<br/>最大 22 MB/秒 | 每 vCore 4 MB/秒<br/>最大 48 MB/s |
| 数据吞吐量（近似） | 100 - 250 MB/s（每个文件）<br/>\*[增加文件大小以获得更好的 IO 性能](#file-io-characteristics-in-general-purpose-tier) | 不受限制。 |
| 存储 IO 延迟（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 内存中 OLTP | 不支持 | 可用，[大小取决于 vCore 数量](#in-memory-oltp-available-space) |
| 最大会话 | 30000 | 30000 |
| [只读副本](sql-database-read-scale-out.md) | 0 | 1（包含于价格中） |

> [!NOTE]
> - **当前可用的实例存储大小**是保留实例大小与已用存储空间之间的差异。
> - 与最大存储大小限制进行比较的实例存储大小同时包括用户和系统数据库中的数据及日志文件大小。 可以使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系统视图来确定数据库使用的空间总量。 错误日志不会持久保存，不包括在大小中。 备份不包括在存储大小中。
> - 常规用途层上的吞吐量和 IOPS 也依赖于不是由托管实例显式限制的[文件大小](#file-io-characteristics-in-general-purpose-tier)。
> - 你可以使用自动故障转移组在不同的 Azure 区域中创建另一个可读副本。
> - 最大实例 IOPS 取决于工作负荷的文件布局和分配。 例如，如果你创建 7 x 1TB 文件，其中每个和7个小文件（小于 128 GB）都具有 500 IOPS，则可以获取每个实例的 38500 IOPS （7x5000 + 7x500）（如果工作负荷可以使用所有文件）。 请注意，某些 IOPS 还用于自动备份。

> [!NOTE]
> 有关详细信息，请查看[本文中托管实例池中的资源限制](sql-database-instance-pools.md#instance-pools-resource-limitations)。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>常规用途层中的文件 IO 特征

在常规用途服务层中，每个数据库文件都获取专用的 IOPS 和吞吐量，具体取决于文件大小。 较大的文件会获得更多的 IOPS 和吞吐量。 下表显示了数据库文件的 IO 特征：

| 文件大小 | > = 0 且 < = 128 GiB | > 128 和 < = 256 GiB | > 256 和 < = 512 GiB | > 0.5 且 < = 1 TiB    | > 1 和 < = 2 TiB    | > 2 和 < = 4 TiB | > 4 和 < = 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 每个文件的 IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500   |
| 每个文件的吞吐量 | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/秒 | 

如果注意到某个数据库文件的 IO 延迟较高，或者发现 IOPS/吞吐量达到了限制，则可以通过[增加文件大小](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)来提高性能。

此外，还有一些实例级别的限制（如最大日志写入吞吐量 22 MB/秒），因此你可能无法在整个日志文件中访问文件，因为你达到了实例吞吐量限制。

## <a name="supported-regions"></a>支持的区域

只能在[受支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中创建托管实例。 若要在当前不受支持的区域中创建托管实例，可以[通过 Azure 门户发送支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)。

## <a name="supported-subscription-types"></a>支持的订阅类型

托管实例目前仅支持以下类型的订阅上的部署：

- [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [云服务提供商 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 订阅者每月 Azure 信用额度的订阅](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>区域资源限制

支持的订阅类型针对每个区域可包含有限的资源数。 对于每个 Azure 区域，托管实例有两个默认限制（可以根据订阅类型的类型，通过在[Azure 门户中创建特殊支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)来增加按需增加）：

- **子网限制**：在单一区域中部署托管实例的子网数上限。
- **vCore unit limit**：可在单个区域中的所有实例之间部署的 vCore 单元的最大数目。 一个 GP vCore 使用一个 vCore 单元，一个 BC vCore 采用4个 vCore 单位。 实例的总数不受限于，只要它在 vCore 单元限制内。

> [!Note]
> 这些限制是默认设置，而不是技术限制。 如果在当前区域中需要更多的托管实例，则可以通过在[Azure 门户中创建特殊支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)来提高限制。 作为替代方法，可以在另一个 Azure 区域中创建新的托管实例，而无需发送支持请求。

下表显示了支持的订阅类型的**默认区域限制**（可以使用下面所述的支持请求扩展默认限制）：

|订阅类型| 托管实例子网的最大数目 | VCore 单元的最大数目 * |
| :---| :--- | :--- |
|即用即付|3|320|
|CSP |8（在某些区域中为 15 * *）|960（在某些区域中为 1440 * *）|
|即用即付开发/测试|3|320|
|Enterprise 开发/测试|3|320|
|EA|8（在某些区域中为 15 * *）|960（在某些区域中为 1440 * *）|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional 和 MSDN 平台|2|32|

\* 在规划部署中，请考虑业务关键（BC）服务层需要四（4）倍于常规用途（GP）服务层的 vCore。 例如： 1 GP vCore = 1 vCore unit 和 1 BC vCore = 4 vCore 单位。 若要简化对默认限制的消耗分析，请在部署托管实例的区域中的所有子网中汇总 vCore 单位，并将结果与订阅类型的实例单位限制进行比较。 **VCore 单位限制的最大数量**适用于区域中的每个订阅。 每个子网没有任何限制，只不过跨多个子网部署的所有 Vcore 的总和必须小于或等于**vCore 单元的最大数目**。

以下区域提供了 \*\* 更大的子网和 vCore 限制：澳大利亚东部、美国东部、美国东部2、北欧、美国中南部、东南亚、英国南部、西欧、美国西部2。

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>为 SQL 托管实例获取更大的配额

如果您在当前区域中需要更多的托管实例，则发送支持请求以便使用 Azure 门户扩展配额。
若要启动获取更大配额的过程，请执行以下操作：

1. 打开“帮助 + 支持”，单击“新建支持请求”。

   ![帮助和支持](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 在新支持请求的“基本信息”选项卡上：
   - 对于“问题类型”，选择“服务和订阅限制(配额)”。
   - 对于“订阅”，请选择自己的订阅。
   - 对于“配额类型”，选择“SQL 数据库托管实例”。
   - 对于“支持计划”，选择自己的支持计划。

     ![问题类型配额](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. 单击“下一步”。
4. 在新支持请求的 "**问题" 选项卡**上：
   - 对于“严重性”，选择问题的严重性级别。
   - 对于“详细信息”，提供有关问题的其他信息，包括错误消息。
   - 对于“文件上传”，附加包含详细信息的文件（最多 4 MB）。

     ![问题详细信息](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有效的请求应包括：
     > - 需要增加订阅限制的区域。
     > - 在配额增加后，现有子网中的每个服务层所需的 Vcore 数（如果需要扩展现有子网。
     > - 新子网中所需的新子网数和每个服务层的 Vcore 总数（如果需要在新子网中部署托管实例）。

5. 单击“下一步”。
6. 在新支持请求的“联系人信息”选项卡上，输入首选联系方式（电子邮件或电话）和联系人详细信息。
7. 单击“创建”。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 有关定价信息，请参阅 [SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](sql-database-managed-instance-get-started.md)。
