---
title: 定价层-Azure Database for MariaDB
description: 了解 Azure Database for MariaDB 的各种定价层，包括计算代、存储类型、存储大小、Vcore、内存和备份保留期。
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 36563e11d7a5fb7cfd5878294c3b83977f6bb619
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772391"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB 定价层

在“基本”、“常规用途”和“内存优化”这三个不同的定价层中，Azure Database for MariaDB 服务器可以在其中的一个定价层中创建。 定价层的差异表现在可以预配的 vCore 中的计算量、每个 vCore 的内存，以及用于存储数据的存储技术。 所有资源都在 MariaDB 服务器级别预配。 一个服务器可以有一个或多个数据库。

|    | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 计算的代 | Gen 5 |Gen 5 | Gen 5 |
| vCore 数 | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| 每个 vCore 的内存 | 2GB | 5 GB | 10 GB |
| 存储大小 | 5 GB 到 1 TB | 5GB 到 4TB | 5GB 到 4TB |
| 存储类型 | Azure 标准存储 | Azure 高级存储器 | Azure 高级存储器 |
| 数据库备份保留期 | 7 到 35 天 | 7 到 35 天 | 7 到 35 天 |

可以从下表着手来选择定价层。

| 定价层 | 目标工作负荷 |
|:-------------|:-----------------|
| 基本 | 需要轻型计算和 I/O 性能的工作负荷。 示例包括用于开发或测试的服务器，或不常使用的小型应用程序。 |
| 常规用途 | 大多数业务工作负荷。此类工作负荷需要均衡的计算和内存以及可缩放的 I/O 吞吐量。 相关示例包括用于托管 Web 和移动应用的服务器，以及其他企业应用程序。|
| 内存优化 | 高性能数据库工作负荷。此类工作负荷需要内存中性能来实现更快的事务处理速度和更高的并发性。 相关示例包括用于处理实时数据的服务器，以及高性能事务性应用或分析应用。|

创建服务器后，只需数秒即可增加或减少 vCore 数和定价层（来回调整基本定价层除外）。 也可在不关闭应用程序的情况下，独立调整存储容量（向上调整）和备份保留期（上下调整）。 创建服务器后，便无法再更改备份存储类型。 有关详细信息，请参阅[缩放资源](#scale-resources)部分。

## <a name="compute-generations-and-vcores"></a>计算代数和 vCore 数

计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 第 5 代逻辑 CPU 基于 Intel E5-2673 v4 (Broadwell) 2.3-GHz 处理器。

## <a name="storage"></a>存储空间

预配的存储是指可供 Azure Database for MariaDB 服务器使用的存储容量。 此存储可用于数据库文件、临时文件、事务日志和 MariaDB 服务器日志。 预配的总存储量也定义了可供服务器使用的 I/O 容量。

|    | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 存储类型 | Azure 标准存储 | Azure 高级存储器 | Azure 高级存储器 |
| 存储大小 | 5 GB 到 1 TB | 5GB 到 4TB | 5GB 到 4TB |
| 存储增量大小 | 1 GB | 1 GB | 1 GB |
| IOPS | 变量 |3 IOPS/GB<br/>至少 100 IOPS<br/>最大 6000 IOPS | 3 IOPS/GB<br/>至少 100 IOPS<br/>最大 6000 IOPS |

你可以在创建服务器的过程中和之后添加额外的存储容量，并允许系统根据工作负荷的存储消耗量自动增长存储。

>[!NOTE]
> 存储只能扩展，而不能减少。

“基本”层不提供 IOPS 保证。 在“常规用途”和“内存优化”定价层中，IOPS 与预配的存储大小按 3:1 的比例缩放。

可以通过 Azure 门户或 Azure CLI 命令监视 I/O 使用情况。 要监视的相关指标是[存储上限、存储百分比、已用存储和 IO 百分比](concepts-monitoring.md)。

### <a name="reaching-the-storage-limit"></a>达到存储限制

对于预配存储不到 100 GB 的服务器，如果可用存储少于 512MB 或预配存储大小的 5%，则会将其标记为只读。 对于预配存储超出 100 GB 的服务器，如果可用存储少于 5 GB，则会将其标记为只读。

例如，如果你预配了 110 GB 的存储，而实际利用率超过 105 GB，则服务器将标记为只读。 或者，如果你预配了 5 GB 的存储，则当可用存储空间小于 256 MB 时，服务器将标记为只读。

当服务试图将服务器标记为只读时，会阻止所有新的写入事务请求，现有的活动事务将继续执行。 当服务器设置为只读时，所有后续写入操作和事务提交均会失败。 读取查询将继续不间断工作。 增加预配的存储后，服务器将准备好再次接受写入事务。

建议启用存储自动增长，或设置警报，以便在服务器存储接近阈值时通知你，因此你可以避免进入只读状态。 有关详细信息，请参阅有关[如何设置警报](howto-alert-metric.md)的文档。

### <a name="storage-auto-grow"></a>存储自动增长

存储自动增长可防止服务器在存储空间外运行并使其变为只读。 如果启用了存储自动增长，则存储将自动增长，而不会影响工作负荷。 对于预配的存储小于 100 GB 的服务器，当可用存储低于预配存储的10% 时，预配的存储大小将增加 5 GB。 对于预配存储超过 100 GB 的服务器，如果可用存储空间低于预配存储大小的10%，则预配的存储大小将增加5%。 以上指定的最大存储限制适用。

例如，如果你预配了 1000 GB 的存储空间，并且实际利用率超过 900 GB，则服务器存储大小将增加到 1050 GB。 或者，如果你预配了 10 GB 的存储空间，则当可用空间小于 1 GB 时，存储大小将增加到 15 GB。

请记住，存储只能扩展，而不能减少。

## <a name="backup"></a>备份

服务自动对服务器进行备份。 备份的最短保留期为七天。 可以设置长达 35 天的保留期。 可以在服务器的生存期内随时对保留期进行调整。 可以在本地冗余备份和异地冗余备份之间进行选择。 异地冗余备份也存储在创建服务器时所在区域的[异地配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中。 这种冗余可以在发生灾难时提供一定级别的保护。 也可获得将服务器还原到任何其他 Azure 区域的功能，前提是该区域提供的服务带有异地冗余备份。 创建服务器后，无法在这两个备份存储选项之间进行更改。

## <a name="scale-resources"></a>缩放资源

创建服务器后，可以单独更改 vCore 数、定价层（来回调整基本定价层除外）、存储量和备份保留期。 创建服务器后，便无法再更改备份存储类型。 向上或向下调整 VCore 数。 备份保留期可以从 7 天到 35 天进行上下调整。 存储大小只能增加。 可以通过门户或 Azure CLI 缩放资源。 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

更改 vCore 数或定价层时，将会创建原始服务器的副本，其分配有新的计算资源。 启动并运行新服务器后，连接将切换到新服务器。 在系统切换到新服务器的短暂期间，无法建立新的连接，所有未提交的连接将会回退。 此时段不定，但大多数情况下短于一分钟。

缩放存储和更改备份保留期是真正的联机操作。 不会造成停机，应用程序不会受影响。 当 IOPS 随已预配存储的大小缩放时，可以通过扩大存储来增加提供给服务器的 IOPS。

## <a name="pricing"></a>价格

有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/mariadb/)。 若要查看所需配置的具体成本，可以单击 [Azure 门户](https://portal.azure.com/#create/Microsoft.MariaDBServer)的“定价层”选项卡，系统就会根据选定的选项显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择“添加项”，展开“数据库”类别，选择“Azure Database for MariaDB”自定义选项。

## <a name="next-steps"></a>后续步骤
- 了解[服务限制](concepts-limits.md)。
- 了解如何[在 Azure 门户中创建 MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
