---
title: 计算和存储选项-Azure Database for MySQL-灵活服务器
description: 本文介绍 Azure Database for MySQL 灵活的服务器中的计算和存储选项。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 8a1b30803494facf6eaabcc3695770d694b4e221
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708673"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 灵活的服务器 (预览版中的计算和存储选项) 

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

可以在三个不同的计算层中的一个级别创建 Azure Database for MySQL 灵活的服务器：可突增、常规用途和内存优化。 计算层通过 B 系列、D 系列和 E 系列使用的基础 VM SKU 来区分。 计算层和大小的选择决定了服务器上可用的内存和 Vcore。 在所有计算层中使用相同的存储技术。 所有资源都在 MySQL 服务器级别预配。 一个服务器可以有一个或多个数据库。

| 资源/层 | **可突增** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| VM 系列| B 系列 | Ddsv4 系列 | Edsv4 系列|
| vCore 数 | 1, 2 | 2，4，8，16，32，48，64 | 2，4，8，16，32，48，64 |
| 每个 vCore 的内存 | 变量 | 4 GiB | 8 GiB * |
| 存储大小 | 5 GiB 到 16 TiB | 5 GiB 到 16 TiB | 5 GiB 到 16 TiB |
| 数据库备份保留期 | 1到35天 | 1到35天 | 1到35天 |

\* 除了 E64ds_v4 (内存优化) SKU，其中包含 504 GB 内存

若要选择计算层，请使用下表作为起点。

| 计算层 | 目标工作负荷 |
|:-------------|:-----------------|
| 可突增 | 最适用于不连续需要完全 CPU 的工作负载。 |
| 常规用途 | 大多数业务工作负荷。此类工作负荷需要均衡的计算和内存以及可缩放的 I/O 吞吐量。 相关示例包括用于托管 Web 和移动应用的服务器，以及其他企业应用程序。|
| 内存优化 | 高性能数据库工作负荷。此类工作负荷需要内存中性能来实现更快的事务处理速度和更高的并发性。 相关示例包括用于处理实时数据的服务器，以及高性能事务性应用或分析应用。|

创建服务器后，计算层、计算大小和存储大小已更改。 计算缩放需要重新启动，并需要60-120 秒，而不需要重新启动存储缩放。 你还可以在备份保持期的最大范围内进行单独调整。 有关详细信息，请参阅[缩放资源](#scale-resources)部分。

## <a name="compute-tiers-size-and-server-types"></a>计算层、大小和服务器类型

可以根据层和大小选择计算资源。 这会确定 Vcore 和内存大小。 Vcore 表示基础硬件的逻辑 CPU。

可用服务器类型的详细规范如下：

| 计算大小         | vCore 数 | 内存大小 (GiB)  | 
|----------------------|--------|-------------------|
| **可突增**        |        |                   | 
| B1s                  | 1      | 1                 |  
| B1ms                 | 1      | 2                 | 
| B2s                  | 2      | 4                 |  
| **常规用途**  |        |                   | 
| D2ds_v4              | 2      | 8                 |  
| D4ds_v4              | 4      | 16                | 
| D8ds_v4              | 8      | 32                | 
| D16ds_v4             | 16     | 64                | 
| D32ds_v4             | 32     | 128               |  
| D48ds_v4             | 48     | 192               |  
| D64ds_v4             | 64     | 256               | 
| **内存优化** |        |                   |
| E2ds_v4              | 2      | 16                |
| E4ds_v4              | 4      | 32                |
| E8ds_v4              | 8      | 64                |
| E16ds_v4             | 16     | 128               |
| E32ds_v4             | 32     | 256               |
| E48ds_v4             | 48     | 384               |
| E64ds_v4             | 64     | 504               |

若要获取有关可用计算系列的详细信息，请参阅适用于可突增的 Azure VM 文档 [ (B 系列) ](../../virtual-machines/sizes-b-series-burstable.md)， [常规用途 (Ddsv4 系列) ](../../virtual-machines/ddv4-ddsv4-series.md)和 [内存优化 (](../../virtual-machines/edv4-edsv4-series.md)) 。

## <a name="storage"></a>存储

预配的存储空间是可用于灵活服务器的存储容量。 存储用于数据库文件、临时文件、事务日志和 MySQL 服务器日志。 在所有计算层中，支持的最小存储为 5 GiB，最大值为 16 TiB。 按 1 GiB 的增量缩放存储，并在创建服务器后扩展存储。

>[!NOTE]
> 存储只能增加，不能减少。

你可以使用存储限制、存储百分比和存储使用的指标，在 Azure Monitor) 中监视 Azure 门户 (的存储消耗量。 请参阅 [监视文章](./concepts-monitoring.md) 了解相关指标。 

### <a name="reaching-the-storage-limit"></a>达到存储限制

当服务器上使用的存储接近设置的限制时，服务器将设置为只读模式，以保护服务器上丢失的所有写入。 如果可用存储小于预配的存储大小的5%，则 GiB 预配存储小于100的服务器将被标记为只读。 GiB 预配存储超过100的服务器在可用存储小于 5 GiB 时标记为 "只读"。

例如，如果你预配了 110 GiB 的存储，而实际利用率超过 105 GiB，则服务器将标记为只读。 或者，如果你预配了 5 GiB 的存储，则当可用存储空间小于 256 MB 时，服务器将标记为只读。

当服务试图将服务器标记为只读时，会阻止所有新的写入事务请求，现有的活动事务将继续执行。 当服务器设置为只读时，所有后续写入操作和事务提交均会失败。 读取查询将继续不间断工作。 

若要使服务器退出只读模式，应在服务器上增加预配的存储空间。 可以使用 Azure 门户或 Azure CLI 来实现此目的。 增加后，服务器将准备好接受写入事务。

我们建议你设置警报，以便在服务器存储接近阈值时通知你，从而可以避免进入只读状态。 请参阅 [监视文章](./concepts-monitoring.md) 了解可用指标。 

建议你 <!--turn on storage auto-grow or to--> 设置警报，以便在服务器存储接近阈值时通知你，因此你可以避免进入只读状态。 有关详细信息，请参阅有关 [如何设置警报](how-to-alert-on-metric.md)的警报文档的文档。

### <a name="storage-auto-grow"></a>存储自动增长

存储自动增长尚不适用于 Azure Database for MySQL 灵活的服务器。

## <a name="iops"></a>IOPS
每个计算大小的最小有效 IOPS 为100，最大有效 IOPS 由以下两个属性确定： 
- 计算：最大有效 IOPS 可能受所选计算大小的最大可用 IOPS 限制。
- 存储：在所有计算层中，IOPS 按照预配的存储大小，以3:1 的比率缩放。

可以通过增加预配的存储或移动到更大的计算大小来扩展可用的有效 IOPS， (如果 IOPS 受到计算) 的限制。 在预览版中，支持的最大有效 IOPS 为 20000 IOPS。

若要详细了解每个计算大小的最大有效 IOPS，请使用计算和存储的组合，如下所示： 

| 计算大小         | 最大有效 IOPS  | 
|----------------------|---------------------|
| **可突增**        |                     |
| B1s                  | 320                 |
| B1ms                 | 640                 |
| B2s                  | 1280                | 
| **常规用途**  |                     |
| D2ds_v4              | 3200                |
| D4ds_v4              | 6400                |
| D8ds_v4              | 12800               |
| D16ds_v4             | 20000               |
| D32ds_v4             | 20000               |
| D48ds_v4             | 20000               | 
| D64ds_v4             | 20000               | 
| **内存优化** |                     | 
| E2ds_v4              | 3200                | 
| E4ds_v4              | 6400                | 
| E8ds_v4              | 12800               | 
| E16ds_v4             | 20000               | 
| E32ds_v4             | 20000               | 
| E48ds_v4             | 20000               | 
| E64ds_v4             | 20000               |  

最大有效 IOPS 取决于每个计算大小的最大可用 IOPS。 请参阅下面的公式，并参考[B 系列](../../virtual-machines/sizes-b-series-burstable.md)、 [Ddsv4 系列](../../virtual-machines/ddv4-ddsv4-series.md)和[Edsv4 系列](../../virtual-machines/edv4-edsv4-series.md)文档中的*最大未缓存磁盘吞吐量： IOPS/MBps* 。

**最大有效 IOPS** = 计算大小的 (最 *大非缓存磁盘吞吐量： IOPS/MBps* ，GiB 中预配的存储 * 3) 

使用 [IO 百分比](./concepts-monitoring.md) 度量值 Azure Monitor) ，可以监视 Azure 门户 (中的 i/o 消耗情况。 如果需要更多 IOPS，则需要了解你是否受到计算大小或预配存储的限制。 适当地缩放服务器的计算或存储设置。

## <a name="backup"></a>Backup

服务自动对服务器进行备份。 你可以选择介于1到35天之间的保留期。 有关备份的详细信息，请参阅 [备份和还原概念一文](concepts-backup-restore.md)。

## <a name="scale-resources"></a>缩放资源

创建服务器后，可以独立更改计算层、计算大小 (Vcore 和内存) 、存储量和备份保留期。 计算大小可以向上或向下缩放。 备份保留期可以向上或向下扩展1到35天。 存储大小只能增加。 可以通过门户或 Azure CLI 来对资源进行缩放。

> [!NOTE]
> 存储大小只能增加。 增加后，将不能返回到更小的存储大小。

更改计算层或计算大小时，服务器将重新启动，以使新的服务器类型生效。 在系统切换到新服务器的短暂期间，无法建立新的连接，所有未提交的连接将会回退。 此窗口会有所不同，但在大多数情况下，介于60-120 秒之间。 

缩放存储和更改备份保留期是联机操作，无需重新启动服务器。

## <a name="pricing"></a>定价

有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/MySQL/)。 若要查看所需配置的开销， [Azure 门户](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) 会根据所选选项，在 " **计算** " 和 "存储" 选项卡上显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) 网站上，选择 **"添加项**"，展开 " **数据库** " 类别，选择 " **Azure Database for MySQL**"，并选择 " **灵活服务器** " 作为部署类型以自定义选项。

如果希望优化服务器成本，可以考虑以下提示：

- 如果计算未充分利用，请将计算层或计算大小缩小 (Vcore) 。
- 如果你的工作负荷不需要从常规用途和内存优化层连续进行完全计算，请考虑切换到可突增计算层。
- 在未使用时停止服务器。
- 如果不需要较长的备份保留期，请缩短备份保留期。

## <a name="next-steps"></a>后续步骤

- 了解如何[在门户中创建 MySQL 服务器](quickstart-create-server-portal.md)。
- 了解 [服务限制](concepts-limitations.md)。