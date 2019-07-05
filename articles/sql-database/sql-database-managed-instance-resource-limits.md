---
title: Azure SQL 数据库资源限制 - 托管实例 | Microsoft Docs
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
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: a0846a7d03cc2f63af6747c8b8514b563c1d4a5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447801"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>概述 Azure SQL 数据库托管实例的资源限制

本文提供的资源限制概述 Azure SQL 数据库托管实例，并提供有关如何请求增加这些限制的信息。

> [!NOTE]
> 有关支持的功能和 T-SQL 语句的差异，请参阅[功能差异](sql-database-features.md)和 [T-SQL 语句支持](sql-database-managed-instance-transact-sql-information.md)。

## <a name="instance-level-resource-limits"></a>实例级别的资源限制

托管的实例具有特征和依赖于底层基础结构和体系结构的资源限制。 这些限制取决于硬件代系和服务层级。

### <a name="hardware-generation-characteristics"></a>硬件代系特性

Azure SQL 数据库托管的实例可以部署在两个硬件代次：Gen4 和 Gen5。 代次的硬件具有不同的特征下, 表中所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬件 | Intel E5-2673 v3 (Haswell) 2.4-GHz 处理器、附加的 SSD vCore = 1 PP（物理核心） | Intel E5-2673 v4 (Broadwell) 2.3-GHz 处理器、快速 NVMe SSD、vCore=1 LP（超线程） |
| vCore 数 | 8、16、24 个 vCore | 4、 8、 16、 24、 32、 40、 64，80 个 Vcore |
| 内存 （内存/内核之比） | 每个 vCore 7 GB | 每个 vCore 5.1 GB |
| 最大内存中 OLTP 内存 | 实例的限制：每个 vCore 3 GB<br/>数据库限制：<br/> -8 核：每个数据库的 8 GB<br/> -16 核心：每个数据库为 20 GB<br/> -24 核心：每个数据库的 36 GB | 实例的限制：每个 vCore 2.5 GB<br/>数据库限制：<br/> -8 核：每个数据库的 13 GB<br/> -16 核心：每个数据库的 32 GB |
| 最大实例存储（常规用途） |  8 TB | 8 TB |
| 最大实例存储（业务关键） | 1 TB | 1 TB、2 TB 或 4 TB，具体取决于核心数 |

> [!IMPORTANT]
> AustraliaEast 区域中不再支持新 Gen4 数据库。

### <a name="service-tier-characteristics"></a>服务层特征

托管的实例都具有两个服务层：“常规用途”和“业务关键”。 这些层提供不同的功能，如下表所述：

| **功能** | **常规用途** | **业务关键** |
| --- | --- | --- |
| vCore 数目\* | 第 4 代：8、16、24<br/>Gen5：4, 8, 16, 24, 32, 40, 64, 80 | Gen4：8、16、24、32 <br/> Gen5：4, 8, 16, 24, 32, 40, 64, 80 |
| 内存 | Gen4：56 GB - 168 GB (7GB/vCore)<br/>Gen5：40.8 GB - 408 GB (5.1GB/vCore) | Gen4：56 GB - 168 GB (7GB/vCore)<br/>Gen5：40.8 GB - 408 GB (5.1GB/vCore) |
| 最大实例存储大小 | -对于 4 个 vcore 数 (仅 Gen5) 2 TB<br/>的有关其他大小 8 TB | Gen4：1 TB <br/> Gen5： <br/>-1 TB 的 4、 8、 16 个 Vcore<br/>- 24 个 vCore 2 TB<br/>- 4 TB（适用于 32、40、64、80 个 vCore） |
| 每个数据库的最大存储 | 由每个实例的最大存储大小决定 | 由每个实例的最大存储大小决定 |
| 每个实例的数据库数目上限 | 100 | 100 |
| 每个实例的数据库文件数目上限 | 最多 280 个 | 每个数据库 32,767 个文件 |
| 数据/日志 IOPS（近似） | 500 - 7,500（每个文件）<br/>\*[取决于文件大小](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K - 110 K (1375/vCore) |
| 日志吞吐量 | 3 MB/s（每个 vCore）<br/>每个实例的最大 22 MB/秒 | 每个 vCore 4 MB/秒<br/>最大为 48 MB/秒（每个实例）|
| 数据吞吐量（近似） | 100 - 250 MB/s（每个文件）<br/>\*[取决于文件大小](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 不适用 |
| IO 延迟（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 最大 tempDB 大小 | 192 - 1,920 GB（每个 vCore 为 24 GB） | 无约束 - 受最大实例存储大小限制 |
| 最大会话数 | 30000 | 30000 |

> [!NOTE]
> - 与最大存储大小限制进行比较的实例存储大小同时包括用户和系统数据库中的数据及日志文件大小。 可以使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系统视图来确定数据库使用的空间总量。 错误日志不会持久保存，不包括在大小中。 备份不包括在存储大小中。
> - 吞吐量和 IOPS 也取决于显式不受托管实例的页大小。

## <a name="supported-regions"></a>支持的区域

可以仅在创建托管的实例[支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要创建的托管的实例目前不支持的区域中，你可以[发送一个通过 Azure 门户的支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)。

## <a name="supported-subscription-types"></a>支持的订阅类型

托管的实例目前支持仅在以下类型的订阅上的部署：

- [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [云服务提供商 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 订阅者每月 Azure 信用额度的订阅](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>区域资源限制

支持的订阅类型针对每个区域可包含有限的资源数。 托管的实例，每个 Azure 区域，具体取决于订阅类型的类型有两个默认限制：

- **子网限制**：在单一区域中部署托管实例的子网数上限。
- **vCore 限制**:最大 vcore 数，可部署在单个区域中的所有实例数。

> [!Note]
> 这些限制是默认设置，不是技术限制。 限制可以增加按需通过创建一个特殊[在 Azure 门户中的支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)如果需要更多托管的实例的当前区域中。 或者，可以在另一个 Azure 区域中创建新的托管的实例，而无需发送支持请求。

下表显示了受支持的订阅的默认区域限制：

|订阅类型| 托管的实例子网的最大数量 | VCore 单位 * 的最大数量 |
| :---| :--- | :--- |
|即用即付|3|320|
|CSP |8 (15 中某些区域 * *)|960 (1440 中某些区域 * *)|
|即用即付开发/测试|3|320|
|Enterprise 开发/测试|3|320|
|EA|8 (15 中某些区域 * *)|960 (1440 中某些区域 * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional 和 MSDN 平台|2|32|

\* 当你计划你的部署时，请考虑： vCore 业务关键 (BC) （由于添加了冗余） 所消耗的 4 倍的容量多于常规用途 (GP) vCore。 因此，对于您的计算，1 个 GP vCore = 1 个 vCore 单元和 1 BC vCore = 4 个 vCore 单位。 若要简化与默认限制您使用的分析，托管的实例部署中，将使用你的订阅类型的实例单位限制结果进行比较的区域中的所有子网间汇总 vCore 单位。 **最大 vCore 单位数**限制适用于区域中的每个订阅。 没有任何限制每个单个子网，但部署跨多个子网的所有 vcore 数之和必须低于或等于**最大 vCore 数**。

\*\* 在以下区域中提供了更大的子网和 vCore 限制：澳大利亚东部、 美国东部、 美国东部 2、 北欧、 美国中南部、 东南亚、 英国南部、 欧洲西部、 美国西部 2。

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>获取更大配额的 SQL 托管实例

如果需要更多的托管的实例在当前区域中，发送支持请求以扩展使用 Azure 门户的配额。
若要启动获取更大配额的过程，请执行以下操作：

1. 打开“帮助 + 支持”，单击“新建支持请求”   。

   ![帮助和支持](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 在新支持请求的“基本信息”选项卡上：
   - 对于“问题类型”，选择“服务和订阅限制(配额)”   。
   - 对于“订阅”，请选择自己的订阅。 
   - 对于“配额类型”，选择“SQL 数据库托管实例”   。
   - 对于“支持计划”，选择自己的支持计划  。

     ![问题类型配额](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. 单击“下一步”。 
4. 上**问题选项卡**用于新的支持请求：
   - 对于“严重性”，选择问题的严重性级别  。
   - 对于“详细信息”，提供有关问题的其他信息，包括错误消息  。
   - 对于“文件上传”，附加包含详细信息的文件（最多 4 MB）  。

     ![问题详细信息](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有效的请求应包括：
     > - 限制需要哪种订阅中增加的区域。
     > - 服务层后配额的现有子网中每个 Vcore，所需的数量的增加 （如果现有子网的任何需要先将其展开。
     > - 所需的新子网数量和每个新子网中的服务级别的 Vcore 的总行数 （如果您需要部署新的子网中的托管的实例）。

5. 单击“下一步”。 
6. 在新支持请求的“联系人信息”选项卡上，输入首选联系方式（电子邮件或电话）和联系人详细信息。
7. 单击**创建**。

## <a name="next-steps"></a>后续步骤

- 托管实例有关的详细信息，请参阅[什么是托管的实例？](sql-database-managed-instance.md)。
- 有关定价信息，请参阅 [SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要了解如何创建第一个托管的实例，请参阅[快速入门指南](sql-database-managed-instance-get-started.md)。
