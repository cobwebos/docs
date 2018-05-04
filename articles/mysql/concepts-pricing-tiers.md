---
title: Azure Database for MySQL 的定价层
description: 本文介绍 Azure Database for MySQL 的定价层。
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 490b162bcab0656388ef0b211ea693809d446346
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database for MySQL 定价层

在“基本”、“常规用途”和“内存优化”这三个不同的定价层中，Azure Database for MySQL 服务器可以在其中的一个定价层中创建。 定价层的差异表现在可以预配的 vCore 中的计算量、每个 vCore 的内存，以及用于存储数据的存储技术。 所有资源都在 MySQL 服务器级别预配。 一个服务器可以有一个或多个数据库。

|    | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 计算的代 | 第 4 代、第 5 代 | 第 4 代、第 5 代 | 第 5 代 |
| vCore 数 | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| 每个 vCore 的内存 | 基线 | “基本”层的 2 倍 | “常规用途”层的 2 倍 |
| 存储大小 | 5 GB 到 1 TB | 5 GB 到 2 TB | 5 GB 到 2 TB |
| 存储类型 | Azure 标准存储 | Azure 高级存储 | Azure 高级存储 |
| 数据库备份保留期 | 7 到 35 天 | 7 到 35 天 | 7 到 35 天 |

可以从下表着手来选择定价层。

| 定价层 | 目标工作负荷 |
|:-------------|:-----------------|
| 基本 | 需要轻型计算和 I/O 性能的工作负荷。 示例包括用于开发或测试的服务器，或不常使用的小型应用程序。 |
| 常规用途 | 大多数业务工作负荷。此类工作负荷需要均衡的计算和内存以及可缩放的 I/O 吞吐量。 相关示例包括用于托管 Web 和移动应用的服务器，以及其他企业应用程序。|
| 内存优化 | 高性能数据库工作负荷。此类工作负荷需要内存中性能来实现更快的事务处理速度和更高的并发性。 相关示例包括用于处理实时数据的服务器，以及高性能事务性应用或分析应用。|

创建服务器后，只需数秒钟即可向上或向下更改 vCore 数（在同一定价层中）。 也可在不关闭应用程序的情况下，独立调整存储容量（向上调整）和备份保留期（上下调整）。 创建服务器之后，不能更改定价层或备份存储类型。 有关详细信息，请参阅[缩放资源](#scale-resources)部分。

## <a name="compute-generations-vcores-and-memory"></a>计算的代、vCore 数和内存

计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 目前提供两代计算（第 4 代和第 5 代）供你选择。 第 4 代逻辑 CPU 基于 Intel E5-2673 v3 (Haswell) 2.4-GHz 处理器。 第 5 代逻辑 CPU 基于 Intel E5-2673 v4 (Broadwell) 2.3-GHz 处理器。 可在以下区域获取第 4 代和第 5 代（“X”表示可用）。 

| **Azure 区域** | **第 4 代** | **第 5 代** |
|:---|:----------:|:--------------------:|
| 美国中部 | X |  |
| 美国东部 | X | X |
| 美国东部 2 | X | X |
| 美国中北部 | X |  |
| 美国中南部 | X | X |
| 美国西部 | X | X |
| 美国西部 2 |  | X |
| 加拿大中部 | X | X |
| 加拿大东部 | X | X |
| 巴西南部 | X | X |
| 北欧 | X | X |
| 欧洲西部 |  | X |
| 英国西部 |  | X |
| 英国南部 |  | X |
| 东亚 | X |  |
| 东南亚 | X | X |
| 澳大利亚东部 |  | X |
| 澳大利亚东南部 |  | X |
| 印度中部 | X |  |
| 印度西部 | X |  |
| 印度南部 |  | X |
| 日本东部 | X | X |
| 日本西部 | X | X |
| 韩国南部 |  | X |

每个 vCore 都预配了特定的内存量，具体取决于定价层。 在增减服务器的 vCore 数时，内存也会按比例增减。 “常规用途”层为每个 vCore 提供的内存量是“基本”层的两倍。 “内存优化”层提供的内存量是“常规用途”层的两倍。

## <a name="storage"></a>存储

预配的存储是指可供 Azure Database for MySQL 服务器使用的存储容量。 此存储可用于数据库文件、临时文件、事务日志和 MySQL 服务器日志。 预配的总存储量也定义了可供服务器使用的 I/O 容量。

|    | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 存储类型 | Azure 标准存储 | Azure 高级存储 | Azure 高级存储 |
| 存储大小 | 5 GB 到 1 TB | 5 GB 到 2 TB | 5 GB 到 2 TB |
| 存储增量大小 | 1 GB | 1 GB | 1 GB |
| IOPS | 变量 |3 IOPS/GB<br/>至少 100 IOPS | 3 IOPS/GB<br/>至少 100 IOPS |

在创建服务器的过程中和之后，可以添加更多的存储容量。 “基本”层不提供 IOPS 保证。 在“常规用途”和“内存优化”定价层中，IOPS 与预配的存储大小按 3:1 的比例缩放。

可以通过 Azure 门户或 Azure CLI 命令监视 I/O 使用情况。 要监视的相关指标是[存储上限、存储百分比、已用存储和 IO 百分比](concepts-monitoring.md)。

## <a name="backup"></a>备份

服务自动对服务器进行备份。 备份的最短保留期为七天。 可以设置长达 35 天的保留期。 可以在服务器的生存期内随时对保留期进行调整。 可以在本地冗余备份和异地冗余备份之间进行选择。 异地冗余备份也存储在创建服务器时所在区域的[异地配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中。 这种冗余可以在发生灾难时提供一定级别的保护。 也可获得将服务器还原到任何其他 Azure 区域的功能，前提是该区域提供的服务带有异地冗余备份。 创建服务器后，无法在这两个备份存储选项之间进行更改。

## <a name="scale-resources"></a>缩放资源

创建服务器之后，可以独立地更改 vCore 数、存储量和备份保留期。 创建服务器之后，不能更改定价层或备份存储类型。 可以在同一定价层中向上或向下调整 VCores 数。 备份保留期可以从 7 天到 35 天进行上下调整。 存储大小只能增加。 可以通过门户或 Azure CLI 缩放资源。 有关使用 Azure CLI 进行缩放的示例，请参阅[使用 Azure CLI 监视和缩放 Azure Database for MySQL 服务器](scripts/sample-scale-server.md)。

更改 vCore 数时，将会使用新的计算分配创建原始服务器的副本。 启动并运行新服务器后，连接将切换到新服务器。 在系统切换到新服务器的短暂期间，无法建立新的连接，所有未提交的连接将会回退。 此时段是可变的，但大多数情况下不到一分钟。

缩放存储和更改备份保留期是真正的联机操作。 不会造成停机，应用程序不会受影响。 当 IOPS 随已预配存储的大小缩放时，可以通过扩大存储来增加提供给服务器的 IOPS。

## <a name="pricing"></a>定价

有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/mysql/)。 若要查看所需配置的具体成本，可以单击 [Azure 门户](https://portal.azure.com/#create/Microsoft.MySQLServer)的“定价层”选项卡，系统就会根据选定的选项显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择“添加项”，展开“数据库”类别，选择“Azure Database for MySQL”自定义选项。

## <a name="next-steps"></a>后续步骤

- 了解如何[在门户中创建 MySQL 服务器](howto-create-manage-server-portal.md)。
- 了解如何[使用 Azure CLI 监视和缩放 Azure Database for MySQL 服务器](scripts/sample-scale-server.md)。
- 了解[服务限制](concepts-limits.md)。
