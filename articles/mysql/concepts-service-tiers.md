---
title: "Azure Database for MySQL 中的定价层 | Microsoft Docs"
description: "Azure Database for MySQL 中的定价层"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 20ca0abab5e17f82b94a31c1b2c9a0942ba9508a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Azure Database for MySQL 选项和性能：了解每个定价层提供的功能
创建 Azure Database for MySQL 服务器时，需确定三个主要选项以配置为该服务器分配的资源。 这些选项会影响服务器的性能和规模。
- 定价层
- 计算单位
- 存储器 (GB)

每个定价层都具有一系列可供选择的性能级别（计算单元），具体取决于工作负荷要求。 更高的性能级别可为旨在提供更高吞吐量的服务器提供附加资源。 可以在定价层内更改服务器的性能级别，几乎没有应用程序停机时间。

> [!IMPORTANT]
> 在服务处于公共预览版状态期间，没有受保证的服务级别协议 (SLA)。

用户在 Azure Database for MySQL 数据库中可具有一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，还可以创建多个数据库来共享资源。 

## <a name="choose-a-pricing-tier"></a>选择一个定价层
在预览中，Azure Database for MySQL 提供两个定价层：基本和标准。 高级层尚不可用，但是即将推出。 

下表提供了最适用于不同应用程序工作负荷的定价层的示例。

| 定价层 | 目标工作负荷 |
| :----------- | :----------------|
| 基本 | 最适合需要在无 IOPS 保证下进行可缩放计算和存储的小型工作负荷。 示例包括用于开发或测试的服务器，或不常使用的小型应用程序。 |
| 标准 | 适用于需要通过高吞吐量实现 IOPS 保证的云应用程序的首选选项。 应用示例包括 Web 或分析应用程序。 |
| 高级 | 最适用于对事务和 IO 需要低延迟的工作负荷。 对许多并发用户提供最佳支持。 适用于支持任务关键型应用程序的数据库。<br />高级定价层尚未提供预览版。 |

若要确定定价层，首先请确定工作负荷是否需要 IOPS 保证。 如果需要，则使用标准定价层。

| 定价层功能 | **基本** | **标准** |
| :------------------------ | :-------- | :----------- |
| 最大计算单元数 | 100 | 800 | 
| 最大总存储 | 1 TB | 1 TB | 
| 存储 IOPS 保证 | 不适用 | 是 | 
| 最大存储 IOPS | 不适用 | 3,000 | 
| 数据库备份保留期 | 7 天 | 35 天 | 

在预览时间范围内，在创建服务器之后无法更改定价层。 将来可以将服务器从某一定价层升级或降级到另一定价层。

## <a name="choose-a-performance-level-compute-units"></a>选择性能级别（计算单元）
为 Azure Database for MySQL 服务器确定了定价层之后，便准备好通过选择所需计算单元数来确定性能级别。 对于对其 Web 和分析型工作负荷需要更高用户并发，并且根据需要进行递增调整的应用程序，良好的起点是 200 或 400 个计算单元。 

计算单元是保证单个 Azure Database for MySQL 服务器可用的 CPU 处理吞吐量的一种度量值。 计算单元是 CPU 和内存资源的混合度量值。  有关详细信息，请参阅[说明计算单元](concepts-compute-unit-and-storage.md)

### <a name="basic-pricing-tier-performance-levels"></a>基本定价层性能级别：

| **性能级别** | **50** | **100** |
| :-------------------- | :----- | :------ |
| 最大计算单元数 | 50 | 100 |
| 所含存储大小 | 50 GB | 50 GB |
| 最大服务器存储大小\* | 1 TB | 1 TB |

### <a name="standard-pricing-tier-performance-levels"></a>标准定价层性能级别：

| **性能级别** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| 最大计算单元数 | 100 | 200 | 400 | 800 |
| 所含存储大小和预配 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS | 125 GB,<br/> 375 IOPS |
| 最大服务器存储大小\* | 1 TB | 1 TB | 1 TB | 1 TB |
| 服务器预配 IOPS 的最大值 | 3,000 IOPS | 3,000 IOPS | 3,000 IOPS | 3,000 IOPS |
| 服务器预配 IOPS 的最大值/GB | 固定为 3 IOPS/GB | 固定为 3 IOPS/GB | 固定为 3 IOPS/GB | 固定为 3 IOPS/GB |

\*最大服务器存储大小指服务器的最大预配存储大小。

## <a name="storage"></a>存储 
存储配置定义可供 Azure Database for MySQL 服务器使用的存储容量。 服务使用的存储包括数据库文件、事务日志和 MySQL 服务器日志。 选择存储配置时，请考虑承载数据库所需的存储大小以及性能要求 (IOPS)。

每个定价层都至少包含某个存储容量（在前面的表格中注明为“包含存储大小”）。 创建服务器时，可以添加额外存储容量（以 125 GB 为增量），直到达到允许的最大存储。 可以独立于计算单元配置来配置额外存储容量。 价格基于选择的存储量而变化。

每个性能级别中的 IOPS 配置都与所选的定价层和存储大小相关。 基本层不提供 IOPS 保证。 在标准定价层中，IOPS 以固定的 3:1 比率，按比例扩展到最大存储。 所含的 125 GB 存储保证预配 IOPS 为 375，其中每个 IO 的大小最多可达 256 KB。 可以选择最大为 1 TB 的额外存储，以保证 3,000 预配 IOPS。

在 Azure 门户中监视指标图或编写 Azure CLI 命令以度量存储和 IOPS 的消耗。 要监视的相关指标是存储空间上限、存储百分比、已用存储和 IO 百分比。

>[!IMPORTANT]
> 在预览中，创建服务器时可选择存储量。 尚不支持更改现有服务器上的存储大小。 

## <a name="scaling-a-server-up-or-down"></a>增加或减少服务器
最初在创建 Azure Database for MySQL 时选择定价层和性能级别。 以后可以在相同定价层的范围内，动态地增加或减少计算单元。 在 Azure 门户中，在服务器的定价层边栏选项卡上滑动计算单元，或按照以下示例对其编写脚本：[使用 Azure CLI 监视和缩放 Azure Database for MySQL 服务器](scripts/sample-scale-server.md)

计算单元的缩放可独立于所选的最大存储大小来进行。

在后台更改数据库的性能级别将在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 在此进程中不会丢失数据。 在我们切换到副本时的短暂瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 此窗口会有所不同，但平均起伏少于 4 秒，而在超过 99%的情况下为不足 30 秒。 如果有很大数量的事务在连接禁用的瞬间正在进行中，则此窗口停留时间可能会更长。

整个缩放进程的持续时间同时取决于更改前后服务器的大小和定价层。 例如，在标准定价层内更改计算单元的服务器应在几分钟内完成。 更改完成之前不会应用服务器的新属性。

## <a name="next-steps"></a>后续步骤
- 有关计算单元的详细信息，请参阅[说明计算单元](concepts-compute-unit-and-storage.md)
- 了解如何[使用 Azure CLI 监视和缩放用于 MySQL 服务器的 Azure 数据库](scripts/sample-scale-server.md)

