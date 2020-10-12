---
title: 配置选项–超大规模 (Citus) -Azure Database for PostgreSQL
description: 用于超大规模 (Citus) 服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/1/2020
ms.openlocfilehash: 8dc70eaeb9e2c2f5d4cdfef37619e4b04217782e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964509"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL –超大规模 (Citus) 配置选项

## <a name="compute-and-storage"></a>计算和存储
 
你可以为辅助角色节点和超大规模 (Citus) 服务器组中的协调器节点单独选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 预配的存储大小指的是可用于超大规模 (Citus) 服务器组中协调器和辅助角色节点的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。
 
| 资源              | 工作器节点           | 协调器节点      |
|-----------------------|-----------------------|-----------------------|
| 计算，Vcore       | 4、8、16、32、64      | 4、8、16、32、64      |
| 内存/vCore，GiB | 8                     | 4                     |
| 存储大小，TiB     | 0.5、1、2             | 0.5、1、2             |
| 存储类型          | 常规用途 (SSD)  | 常规用途 (SSD)  |
| IOPS                  | 最多 3 IOPS/GiB      | 最多 3 IOPS/GiB      |

单个超大规模 (Citus) 节点中的 RAM 总量取决于所选数目的 Vcore。

| vCore 数 | 一个辅助节点，GiB RAM | 协调器节点，GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

预配的总存储量也定义了可用于每个辅助角色和协调器节点的 i/o 容量。

| 存储大小，TiB | 最大 IOPS |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6148        |

对于整个超大规模 (Citus) 群集，聚合的 IOPS 将使用以下值：

| 辅助角色节点 | 0.5 TiB，总 IOPS | 1 TiB，总 IOPS | 2 TiB，总 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12296            |
| 3            | 4608               | 9216             | 18444            |
| 4            | 6,144               | 12,288            | 24592            |
| 5            | 7680               | 15,360            | 30740            |
| 6            | 9216               | 18432            | 36888            |
| 7            | 10752              | 21504            | 43036            |
| 8            | 12,288              | 24576            | 49184            |
| 9            | 13824              | 27648            | 55332            |
| 10           | 15,360              | 30720            | 61480            |
| 11           | 16896              | 33,792            | 67628            |
| 12           | 18432              | 36864            | 73776            |
| 13           | 19968              | 39936            | 79924            |
| 14           | 21504              | 43008            | 86072            |
| 15           | 23040              | 46080            | 92220            |
| 16           | 24576              | 49152            | 98368            |
| 17           | 26112              | 52224            | 104516           |
| 18           | 27648              | 55296            | 110664           |
| 19           | 29184              | 58368            | 116812           |
| 20           | 30720              | 61440            | 122960           |

## <a name="regions"></a>区域
以下 Azure 区域提供了超大规模 (Citus) 服务器组：

* 美洲：
    * 加拿大中部
    * 美国中部
    * 美国东部
    * 美国东部 2
    * 美国中北部
    * 美国西部 2
* 亚太：
    * 澳大利亚东部
    * Japan East
    * 韩国中部
    * 东南亚
* 东欧
    * 北欧
    * 英国南部
    * 西欧

在所有 Azure 订阅中，可能最初不会激活其中一些区域。 如果要使用上述列表中的某个区域，并且在订阅中看不到它，或者要使用不在此列表中的区域，请打开 [支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="limits-and-limitations"></a>限制和局限性

以下部分介绍了超大规模 (Citus) 服务中的容量和功能限制。

### <a name="maximum-connections"></a>最大连接数

每个 PostgreSQL 连接 (甚至空闲的连接) 使用至少 10 MB 的内存，因此，必须限制同时连接数。 下面是我们选择使节点保持正常运行的限制：

* 协调器节点
   * 最大连接数：300
   * 最大用户连接数：297
* 工作器节点
   * 最大连接数：600
   * 最大用户连接数：597

如果尝试连接超过这些限制，则会失败并出现错误。 系统为监视节点预留三个连接，这就是为什么用户查询可以使用三个以上的连接，而不是连接总数。

建立新的连接将耗费时间。 这适用于大多数应用程序，这些应用程序请求很多生存期较短的连接。 建议使用连接池的连接，以减少空闲事务并重复使用现有连接。 若要了解详细信息，请访问我们的 [博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)。

### <a name="storage-scaling"></a>存储缩放

协调器和辅助角色节点上的存储可 (增加) 增加，但不能降低 (降低) 。

### <a name="storage-size"></a>存储大小

协调器和工作节点上最多支持2个 TiB 的存储。 请参阅 [上述](#compute-and-storage) 适用于节点和群集大小的存储选项和 IOPS 计算。

## <a name="pricing"></a>定价
有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看所需配置的开销， [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) 会根据所选选项，在 " **配置** " 选项卡上显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) 网站上，选择 **"添加项**"，展开 " **数据库** " 类别，然后选择 " **Azure Database for PostgreSQL –超大规模" (") ** " 以自定义选项。
 
## <a name="next-steps"></a>后续步骤
了解如何 [在门户中创建超大规模 (Citus) 服务器组](quickstart-create-hyperscale-portal.md)。
