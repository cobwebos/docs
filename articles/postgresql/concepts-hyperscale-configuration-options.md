---
title: Azure Database for PostgreSQL –超大规模（Citus）性能选项
description: 超大规模（Citus）服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2b848827d3d2017e5e787989553c0bf1e26d48e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482629"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL –超大规模（Citus）性能选项

## <a name="compute-and-storage"></a>计算和存储
 
你可以为辅助角色节点和超大规模（Citus）服务器组中的协调器节点单独选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 预配的存储大小指的是可用于超大规模（Citus）服务器组中协调器和辅助角色节点的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。 预配的总存储量也定义了可用于每个辅助角色和协调器节点的 i/o 容量。
 
|                       | 工作器节点           | 协调器节点      |
|-----------------------|-----------------------|-----------------------|
| 计算，Vcore       | 4、8、16、32、64      | 4、8、16、32、64      |
| 内存/vCore，GiB | 8                     | 4                     |
| 存储大小，TiB     | 0.5、1、2             | 0.5、1、2             |
| 存储类型          | 常规用途（SSD） | 常规用途（SSD） |
| IOPS                  | 最多 3 IOPS/GiB      | 最多 3 IOPS/GiB      |


## <a name="regions"></a>Regions
超大规模（Citus）服务器组在以下 Azure 区域中提供：

* 美洲：
    * 加拿大中部 *
    * 美国东部
    * 美国东部 2
    * 美国中北部 *
    * 美国西部 2
* 亚太：
    * 澳大利亚东部 *
    * 东南亚
* 东欧
    * 北欧
    * 英国南部
    * 欧洲西部

带有星号（\*）的区域尚不支持[高可用性](concepts-hyperscale-high-availability.md)。

## <a name="pricing"></a>定价
有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看所需配置的开销， [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)会根据所选选项，在 "**配置**" 选项卡上显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择 **"添加项**"，展开 "**数据库**" 类别，然后选择 " **Azure Database for PostgreSQL –超大规模（Citus）** " 以自定义选项。
 
## <a name="next-steps"></a>后续步骤
了解如何[在门户中创建超大规模（Citus）服务器组](quickstart-create-hyperscale-portal.md)。
