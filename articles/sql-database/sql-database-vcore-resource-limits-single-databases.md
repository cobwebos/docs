---
title: vCore 资源限制-单个数据库
description: 本页介绍 Azure SQL 数据库中单一数据库的一些常见 vCore 资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/11/2019
ms.openlocfilehash: 4455181ddf69613ba07bcaeedb26273a4bb5a74d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647843"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>使用 vCore 购买模型的单一数据库的资源限制

本文提供使用 vCore 购买模型的 Azure SQL 数据库单一数据库的详细资源限制。

有关 SQL 数据库服务器上单个数据库的 DTU 购买模型限制，请参阅[Sql 数据库服务器上的资源限制概述](sql-database-resource-limits-database-server.md)。

可通过 [Azure 门户](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 为单一数据库设置服务层级、计算大小和存储量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放单个数据库](sql-database-single-database-scale.md)。

## <a name="general-purpose---serverless-compute---gen5"></a>常规用途-无服务器计算-Gen5

[无服务器计算层](sql-database-serverless.md)目前仅在 Gen5 硬件上可用。

### <a name="gen5-compute-generation-part-1"></a>Gen5 计算生成（第1部分）

|计算大小|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|
|最小值-最大 Vcore|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|最小值-最大内存（GB）|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|最小自动暂停延迟（分钟）|60|60|60|60|60|
|列存储支持|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (GB)|512|1024|1024|1024|1536|
|最大日志大小 (GB)|154|307|307|307|461|
|TempDB 最大数据大小（GB）|32|64|128|192|256|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS *|320|640|1280|1920|2560|
|最大对数速率（MBps）|3.8|7.5|15|22.5|30|
|最大并发工作线程数（请求数）|75|150|300|450|600|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|
|副本数|第|第|第|第|第|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|N/A|N/A|N/A|N/A|N/A|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>Gen5 计算生成（第2部分）

|计算大小|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|
|最小值-最大 Vcore|1.25-10|1.50-12|1.75-14|2.00-16|
|最小值-最大内存（GB）|3.75-30|4.50-36|5.25-42|6.00-48|
|最小自动暂停延迟（分钟）|60|60|60|60|
|列存储支持|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|
|最大数据大小 (GB)|1536|3072|3072|3072|
|最大日志大小 (GB)|461|461|461|922|
|TempDB 最大数据大小（GB）|320|384|448|512|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS *|3200|3840|4480|5120|
|最大对数速率（MBps）|30|30|30|30|
|最大并发工作线程数（请求数）|750|900|1050|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|
|副本数|第|第|第|第|
|Multi-AZ|N/A|N/A|N/A|N/A|
|读取横向扩展|N/A|N/A|N/A|N/A|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="hyperscale---provisioned-compute---gen4"></a>超大规模预配的计算-Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 计算生成（第1部分）

|性能级别|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|第|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)规格|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100|
|最大日志大小 (TB)|第 |第 |第 |第 |第 |第 |
|TempDB 最大数据大小（GB）|32|64|96|128|160|192|
|存储类型| [备注1](#notes) |[备注1](#notes)|[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |
|最大数据 IOPS *|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|
|IO 延迟（近似）|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|辅助副本|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|是|是|是|是|是|是|
|备份存储保留|7 天|7 天|7 天|7 天|7 天|7 天|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 计算生成（第2部分）

|性能级别|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|159.5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)规格|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100 |
|最大日志大小 (TB)|第 |第 |第 |第 |第 |第 |
|TempDB 最大数据大小（GB）|224|256|288|320|512|768|
|存储类型| [备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |
|最大数据 IOPS *|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|
|IO 延迟（近似）|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|
|最大并发工作线程数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|辅助副本|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|是|是|是|是|是|是|
|备份存储保留|7 天|7 天|7 天|7 天|7 天|7 天|
|||

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="hyperscale---provisioned-compute---gen5"></a>超大规模预配的计算-Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 计算生成（第1部分）

|性能级别|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|2|4|6|8|10|12|14|
|内存 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)规格|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100 |100|
|最大日志大小 (TB)|第 |第 |第 |第 |第 |第 |第 |
|TempDB 最大数据大小（GB）|64|128|192|256|320|384|448|
|存储类型| [备注1](#notes) |[备注1](#notes)|[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |
|最大数据 IOPS *|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|
|IO 延迟（近似）|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|1400|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|辅助副本|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|是|是|是|是|是|是|是|
|备份存储保留|7 天|7 天|7 天|7 天|7 天|7 天|7 天|
|||

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>Gen5 计算生成（第2部分）

|性能级别|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|16|18|20|24|32|40|80|
|内存 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)规格|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|3倍内存|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (TB)|100 |100 |100 |100 |100 |100 |100 |
|最大日志大小 (TB)|第 |第 |第 |第 |第 |第 |第 |
|TempDB 最大数据大小（GB）|512|576|640|768|1024|1280|2560|
|存储类型| [备注1](#notes) |[备注1](#notes)|[备注1](#notes)|[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |[备注1](#notes) |
|最大数据 IOPS *|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|[备注2](#notes)|
|IO 延迟（近似）|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|[备注3](#notes)|
|最大并发工作线程数（请求数）|200|400|800|1600|2400|3200|8000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|辅助副本|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|是|是|是|是|是|是|是|
|备份存储保留|7 天|7 天|7 天|7 天|7 天|7 天|7 天|
|||

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

#### <a name="notes"></a>说明

**注释 1**：超大规模是具有单独计算和存储组件的多层体系结构：[超大规模服务层体系结构](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**备注 2**：超大规模多层体系结构在多个级别具有缓存。 有效 IOPS 将取决于工作负荷。

**备注 3**：对计算副本上基于 RBPEX SSD 的缓存中的数据的延迟为1-2 毫秒，这将缓存最常使用的数据页。 从页面服务器中检索到的数据的延迟较高。

## <a name="general-purpose---provisioned-compute---gen4"></a>常规用途预配的计算-Gen4

> [!IMPORTANT]
> 澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。

### <a name="gen4-compute-generation-part-1"></a>Gen4 计算生成（第1部分）

|计算大小|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|第|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|
|最大日志大小 (GB)|307|307|461|461|461|922|
|TempDB 最大数据大小（GB）|32|64|96|128|160|192|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS *|320|640|960|1280|1600|1920|
|最大对数速率（MBps）|3.75|7.5|11.25|15|18.75|22.5|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|第|第|第|第|第|第|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|N/A|N/A|N/A|N/A|N/A|N/A|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen4-compute-generation-part-2"></a>Gen4 计算生成（第2部分）

|计算大小|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|159.5|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (GB)|3072|3072|3072|3072|4096|4096|
|最大日志大小 (GB)|922|922|922|922|1229|1229|
|TempDB 最大数据大小（GB）|224|256|288|320|512|768|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）
|最大数据 IOPS *|2240|2560|2880|3200|5120|7680|
|最大对数速率（MBps）|26.3|30|30|30|30|30|
|最大并发工作线程数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|第|第|第|第|第|第|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|N/A|N/A|N/A|N/A|N/A|N/A|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---gen5"></a>常规用途预配的计算-Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 计算生成（第1部分）

|计算大小|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|2|4|6|8|10|12|14|
|内存 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|3072|
|最大日志大小 (GB)|307|307|461|461|461|922|922|
|TempDB 最大数据大小（GB）|64|128|192|256|320|384|384|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS *|640|1280|1920|2560|3200|3840|4480|
|最大对数速率（MBps）|7.5|15|22.5|30|30|30|30|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|1400|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|第|第|第|第|第|第|第|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>Gen5 计算生成（第2部分）

|计算大小|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|16|18|20|24|32|40|80|
|内存 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|最大数据大小 (GB)|3072|3072|3072|4096|4096|4096|4096|
|最大日志大小 (GB)|922|922|922|1229|1229|1229|1229|
|TempDB 最大数据大小（GB）|512|576|640|768|1024|1280|2560|
|存储类型|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS *|5120|5760|6400|7680|10240|12800|25600|
|最大对数速率（MBps）|30|30|30|30|30|30|30|
|最大并发工作线程数（请求数）|1600|1800|2000|2400|3200|4000|8000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|第|第|第|第|第|第|第|
|Multi-AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|读取横向扩展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>常规用途预配的 Fsv2 系列

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 系列计算生成（预览）

|计算大小|GP_Fsv2_72|
|:--- | --: |
|计算的代|Fsv2 系列|
|vCore 数|72|
|内存 (GB)|136.2|
|列存储支持|是|
|内存中 OLTP 存储 (GB)|N/A|
|最大数据大小 (GB)|4096|
|最大日志大小 (GB)|1024|
|TempDB 最大数据大小（GB）|333|
|存储类型|远程 SSD|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|最大数据 IOPS *|23040|
|最大对数速率（MBps）|30|
|最大并发工作线程数（请求数）|3600|
|最大并发会话数|30,000|
|副本数|第|
|Multi-AZ|N/A|
|读取横向扩展|N/A|
|随附的备份存储|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---gen4"></a>关键业务预配的计算-Gen4

> [!IMPORTANT]
> 澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。

### <a name="gen4-compute-generation-part-1"></a>Gen4 计算生成（第1部分）

|计算大小|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|第|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|第|2|3|4|5|6|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|最大数据大小 (GB)|1024|1024|1024|1024|1024|1024|
|最大日志大小 (GB)|307|307|307|307|307|307|
|TempDB 最大数据大小（GB）|32|64|96|128|160|192|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS *|4,000|8,000|12,000|16,000|20,000|24,000|
|最大对数速率（MBps）|8|16|24|32|40|48|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|
|最大并发登录数|200|400|600|800|1000|1200|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen4-compute-generation-part-2"></a>Gen4 计算生成（第2部分）

|计算大小|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|计算的代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 数|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|159.5|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|7|8|9.5|11|20|36|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|最大数据大小 (GB)|1024|1024|1024|1024|1024|1024|
|最大日志大小 (GB)|307|307|307|307|307|307|
|TempDB 最大数据大小（GB）|224|256|288|320|512|768|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS |28,000|32,000|36,000|40,000|64,000|76,800|
|最大对数速率（MBps）|56|64|64|64|64|64|
|最大并发工作线程数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发登录数（请求数）|1400|1600|1800|2000|3200|4800|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---gen5"></a>关键业务预配的计算-Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 计算生成（第1部分）

|计算大小|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|2|4|6|8|10|12|14|
|内存 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|3072|
|最大日志大小 (GB)|307|307|461|461|461|922|922|
|TempDB 最大数据大小（GB）|64|128|192|256|320|384|448|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS *|8000|16,000|24,000|32,000|40,000|48000|56000|
|最大对数速率（MBps）|24|48|72|96|96|96|96|
|最大并发工作线程数（请求数）|200|400|600|800|1000|1200|1400|
|最大并发登录数|200|400|600|800|1000|1200|1400|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>Gen5 计算生成（第2部分）

|计算大小|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|计算的代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCore 数|16|18|20|24|32|40|80|
|内存 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|最大数据大小 (GB)|3072|3072|3072|4096|4096|4096|4096|
|最大日志大小 (GB)|922|922|922|1229|1229|1229|1229|
|TempDB 最大数据大小（GB）|512|576|640|768|1024|1280|2560|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS *|64,000|72000|80,000|96000|128000|160,000|204800|
|最大对数速率（MBps）|96|96|96|96|96|96|96|
|最大并发工作线程数（请求数）|1600|1800|2000|2400|3200|4000|8000|
|最大并发登录数|1600|1800|2000|2400|3200|4000|8000|
|最大并发会话数|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|副本数|4|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---m-series"></a>关键业务预配的计算-M 系列

### <a name="m-series-compute-generation-preview"></a>M 系列计算生成（预览）

|计算大小|GP_M_128|
|:--- | --: |
|计算的代|M 系列|
|vCore 数|128|
|内存 (GB)|3767|
|列存储支持|是|
|内存中 OLTP 存储 (GB)|481|
|最大数据大小 (GB)|4096|
|最大日志大小 (GB)|2048|
|TempDB 最大数据大小（GB）|4096|
|存储类型|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|最大数据 IOPS *|204800|
|最大对数速率（MBps）|192|
|最大并发工作线程数（请求数）|12800|
|最大并发会话数|30000|
|副本数|4|
|Multi-AZ|是|
|读取横向扩展|是|
|随附的备份存储|1 倍数据库大小|

\* IO 大小的最大值，范围介于 8 KB 到 64 KB 之间。 实际 IOPS 依赖于工作负荷。 有关详细信息，请参阅[数据 IO 调控](sql-database-resource-limits-database-server.md#resource-governance)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="next-steps"></a>后续步骤

- 有关单个数据库的 DTU 资源限制，请参阅[使用 DTU 购买模型的单一数据库的资源限制](sql-database-dtu-resource-limits-single-databases.md)
- 有关弹性池的 vCore 资源限制，请参阅[使用 vCore 购买模型的弹性池的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用 DTU 购买模型的弹性池的资源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 有关托管实例的资源限制，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关数据库服务器上的资源限制的信息，请参阅 [SQL 数据库服务器资源限制概述](sql-database-resource-limits-database-server.md)了解有关服务器级别和订阅级别限制的信息。
