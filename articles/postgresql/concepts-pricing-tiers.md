---
title: Azure Database for PostgreSQL - 单一服务器的定价层
description: 本文介绍了 Azure Database for PostgreSQL - 单一服务器的定价层。
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5f60a2786a87f4bd9be1f4a9e2a7a222e097b2e1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448077"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器中的定价层

可以在以下三个不同的定价层之一中创建 Azure Database for PostgreSQL 服务器：“基本”、“常规用途”和“内存优化”。 定价层的差异表现在可以预配的 vCore 中的计算量、每个 vCore 的内存，以及用于存储数据的存储技术。 所有资源都在 PostgreSQL 服务器级别预配。 一个服务器可以有一个或多个数据库。

|    | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 计算的代 | 第 4 代、第 5 代 | 第 4 代、第 5 代 | 第 5 代 |
| vCore 数 | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| 每个 vCore 的内存 | 2 GB | 5 GB | 10 GB |
| 存储大小 | 5 GB 到 1 TB | 5GB 到 4TB | 5GB 到 4TB |
| 存储类型 | Azure 标准存储 | Azure 高级存储 | Azure 高级存储 |
| 数据库备份保留期 | 7 到 35 天 | 7 到 35 天 | 7 到 35 天 |

可以从下表着手来选择定价层。

| 定价层 | 目标工作负荷 |
|:-------------|:-----------------|
| 基本 | 需要轻型计算和 I/O 性能的工作负荷。 示例包括用于开发或测试的服务器，或不常使用的小型应用程序。 |
| 常规用途 | 大多数业务工作负荷。此类工作负荷需要均衡的计算和内存以及可缩放的 I/O 吞吐量。 相关示例包括用于托管 Web 和移动应用的服务器，以及其他企业应用程序。|
| 内存优化 | 高性能数据库工作负荷。此类工作负荷需要内存中性能来实现更快的事务处理速度和更高的并发性。 相关示例包括用于处理实时数据的服务器，以及高性能事务性应用或分析应用。|

创建服务器后，只需数秒即可增加或减少 vCore 数、硬件生成和定价层（来回调整基本定价层除外）。 也可在不关闭应用程序的情况下，独立调整存储容量（向上调整）和备份保留期（上下调整）。 创建服务器后，便无法再更改备份存储类型。 有关详细信息，请参阅[缩放资源](#scale-resources)部分。

## <a name="compute-generations-and-vcores"></a>计算代数和 vCore 数

计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 中国东部 1、 1 中国北部、 美国 DoD 中部和美国 DoD 东部利用第 4 代逻辑 Cpu 基于 Intel E5 2673 v3 (Haswell) 2.4-GHz 处理器。 所有其他区域使用第 5 代逻辑 Cpu 基于 Intel E5 2673 v4 (Broadwell) 2.3 GHz 处理器。

## <a name="storage"></a>存储

预配的存储是指可供 Azure Database for PostgreSQL 服务器使用的存储容量。 此存储用于数据库文件、临时文件、事务日志和 PostgreSQL 服务器日志。 预配的总存储量也定义了可供服务器使用的 I/O 容量。

|    | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 存储类型 | Azure 标准存储 | Azure 高级存储 | Azure 高级存储 |
| 存储大小 | 5 GB 到 1 TB | 5GB 到 4TB | 5GB 到 4TB |
| 存储增量大小 | 1 GB | 1 GB | 1 GB |
| IOPS | 变量 |3 IOPS/GB<br/>至少 100 IOPS<br/>最大 6000 IOPS | 3 IOPS/GB<br/>至少 100 IOPS<br/>最大 6000 IOPS |

您可以添加更多存储容量期间和之后创建服务器，并允许系统增长自动根据工作负荷的存储消耗的存储。 “基本”层不提供 IOPS 保证。 在“常规用途”和“内存优化”定价层中，IOPS 与预配的存储大小按 3:1 的比例缩放。

可以通过 Azure 门户或 Azure CLI 命令监视 I/O 使用情况。 要监视的相关指标是[存储上限、存储百分比、已用存储和 IO 百分比](concepts-monitoring.md)。

### <a name="large-storage-preview"></a>较大的存储 （预览版）

我们正在我们常规用途和内存优化层中增加的存储限制。 新创建的服务器的选择中加入预览版可以预配最多 16 TB 的存储空间。 IOPS 3:1 比率较多达 20,000 IOPS 进行缩放。 与当前已公开发布存储一样，您可以服务器，在创建后添加更多存储容量，并允许系统增长自动根据工作负荷的存储消耗的存储。

|              | **常规用途** | **内存优化** |
|:-------------|:--------------------|:---------------------|
| 存储类型 | Azure 高级存储 | Azure 高级存储 |
| 存储大小 | 32 GB 到 16 TB| 32 到 16 TB |
| 存储增量大小 | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>至少 100 IOPS<br/>最大 20,000 IOPS | 3 IOPS/GB<br/>至少 100 IOPS<br/>最大 20,000 IOPS |

> [!IMPORTANT]
> 较大的存储当前处于公共预览版形式在以下区域：美国东部、 美国东部 2、 美国中部、 美国西部、 欧洲北部、 欧洲西部、 英国南部、 英国西部、 亚洲东南部、 东亚、 日本东部、 日本西部、 韩国中部、 韩国南部、 澳大利亚东部、 澳大利亚东南部。
>
> 较大的存储预览版目前不支持：
>
> * 通过虚拟网络服务终结点的传入连接
> * 异地冗余备份
> * 只读副本

### <a name="reaching-the-storage-limit"></a>达到存储限制

使用预配的 100 GB 存储空间小于服务器都标记为只读的可用存储，是否小于 512 MB 或 5%的预配的存储大小。 具有多个预配的 100 GB 存储空间的服务器都标记为只读，仅当的可用存储，为不超过 5 GB。

例如，如果你已设置为 110 GB 的存储空间，并且实际使用率超过 105 GB，将服务器标记为只读。 或者，如果已预配 5 GB 的存储，服务器是标记为只读时免费存储空间达到小于 512 MB。

服务器设置为只读时，所有现有会话都将断开连接，且未提交的事务会回退。 任何后续写入操作和事务提交均会失败。 所有后续读取查询将不间断工作。  

可增加服务器预配存储量，也可在读写模式下启动新会话并删除数据以回收空闲存储。 运行 `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` 将当前会话设置为读写模式。 为避免数据损坏，请勿在服务器仍处于只读状态时执行任何写入操作。

我们建议你在存储上启用自动增长或要将警报设置为你的服务器存储即将达到阈值时通知你因此可以避免进入只读状态。 有关详细信息，请参阅有关[如何设置警报](howto-alert-on-metric.md)的文档。

### <a name="storage-auto-grow"></a>存储自动增长

如果存储自动增长已启用，存储会自动增长而不会影响工作负荷。 对于小于 100 GB，预配存储的服务器，预配的存储大小被增加 5 GB 的可用存储低于 1 GB 或 10%的预配的存储中的较大时，就立即。 对于包含多个 100 GB 的预配的存储的服务器，预配的存储大小被增加 5%时可用的存储空间低于 5%的预配的存储大小。 如上所示的最大存储限制适用。

例如，如果在预配 1000 GB 的存储空间，并实际使用率超过 950 GB，服务器存储大小增加到了 1050 GB。 或者，如果已预配 10 GB，存储大小是存储的增加到 15 GB 免费小于 1 GB 的存储空间时。

## <a name="backup"></a>备份

服务自动对服务器进行备份。 备份的最短保留期为七天。 可以设置长达 35 天的保留期。 可以在服务器的生存期内随时对保留期进行调整。 可以在本地冗余备份和异地冗余备份之间进行选择。 异地冗余备份也存储在创建服务器时所在区域的[异地配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中。 这种冗余可以在发生灾难时提供一定级别的保护。 也可获得将服务器还原到任何其他 Azure 区域的功能，前提是该区域提供的服务带有异地冗余备份。 创建服务器后，无法在这两个备份存储选项之间进行更改。

## <a name="scale-resources"></a>缩放资源

创建服务器后，可以单独更改 vCore 数、硬件生成、定价层（来回调整基本定价层除外）、存储量和备份保持期。 创建服务器后，便无法再更改备份存储类型。 可以增加或减少 VCore 数。 备份保留期可以从 7 天到 35 天进行上下调整。 存储大小只能增加。 可以通过门户或 Azure CLI 缩放资源。 有关使用 Azure CLI 进行缩放的示例，请参阅[使用 Azure CLI 监视和缩放 Azure Database for PostgreSQL 服务器](scripts/sample-scale-server-up-or-down.md)。

更改 vCore 数、硬件生成或定价层时，将会创建原始服务器的副本，其分配有新的计算资源。 启动并运行新服务器后，连接将切换到新服务器。 在系统切换到新服务器的短暂期间，无法建立新的连接，所有未提交的连接将会回退。 此时段不定，但大多数情况下短于一分钟。

缩放存储和更改备份保留期是真正的联机操作。 不会造成停机，应用程序不会受影响。 当 IOPS 随已预配存储的大小缩放时，可以通过扩大存储来增加提供给服务器的 IOPS。

## <a name="pricing"></a>定价

有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/PostgreSQL/)。 若要查看所需配置的具体成本，可以单击 [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)的“定价层”选项卡，系统就会根据选定的选项显示每月成本。  如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择“添加项”  ，展开“数据库”  类别，选择“Azure Database for PostgreSQL”  自定义选项。

## <a name="next-steps"></a>后续步骤

- 了解如何[在门户中创建 PostgreSQL 服务器](tutorial-design-database-using-azure-portal.md)。
- 了解[服务限制](concepts-limits.md)。 
- 了解如何[使用只读副本进行横向扩展](howto-read-replicas-portal.md)。
