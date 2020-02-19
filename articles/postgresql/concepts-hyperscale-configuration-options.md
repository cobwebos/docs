---
title: 性能选项–超大规模（Citus）-Azure Database for PostgreSQL
description: 超大规模（Citus）服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462405"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL –超大规模（Citus）性能选项

## <a name="compute-and-storage"></a>计算和存储
 
你可以为辅助角色节点和超大规模（Citus）服务器组中的协调器节点单独选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 预配的存储大小指的是可用于超大规模（Citus）服务器组中协调器和辅助角色节点的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。 预配的总存储量也定义了可用于每个辅助角色和协调器节点的 i/o 容量。
 
|                       | 辅助角色节点           | 协调器节点      |
|-----------------------|-----------------------|-----------------------|
| 计算，Vcore       | 4、8、16、32、64      | 4、8、16、32、64      |
| 内存/vCore，GiB | 8                     | 4                     |
| 存储大小，TiB     | 0.5、1、2             | 0.5、1、2             |
| 存储类型          | 常规用途（SSD） | 常规用途（SSD） |
| IOPS                  | 最多 3 IOPS/GiB      | 最多 3 IOPS/GiB      |


## <a name="regions"></a>区域
超大规模（Citus）服务器组在以下 Azure 区域中提供：

* 美洲：
    * 加拿大中部
    * 美国中部
    * 美国东部
    * 美国东部 2
    * 美国中北部
    * 美国西部 2
* 亚太：
    * 澳大利亚东部
    * 日本东部
    * 韩国中部
    * 东南亚
* 东欧
    * 北欧
    * 英国南部
    * 西欧

在所有 Azure 订阅中，可能最初不会激活其中一些区域。 如果要使用上述列表中的某个区域，并且在订阅中看不到它，或者要使用不在此列表中的区域，请打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="pricing"></a>定价
有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看所需配置的开销， [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)会根据所选选项，在 "**配置**" 选项卡上显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择 **"添加项**"，展开 "**数据库**" 类别，然后选择 " **Azure Database for PostgreSQL –超大规模（Citus）** " 以自定义选项。
 
## <a name="next-steps"></a>后续步骤
了解如何[在门户中创建超大规模（Citus）服务器组](quickstart-create-hyperscale-portal.md)。
