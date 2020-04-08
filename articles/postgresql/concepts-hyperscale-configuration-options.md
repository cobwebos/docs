---
title: 配置选项 • 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 超大规模（Citus）服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804582"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>用于后格雷SQL的 Azure 数据库 – 超大规模（Citus） 配置选项

## <a name="compute-and-storage"></a>计算和存储
 
您可以为超大规模 （Citus） 服务器组中的工作节点和协调器节点单独选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 用于预配的存储大小是指超大规模 （Citus） 服务器组中的协调员和辅助节点可用的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。
 
|                       | 工作器节点           | 协调员节点      |
|-----------------------|-----------------------|-----------------------|
| 计算，vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| 每个 vCore 的内存，GiB | 8                     | 4                     |
| 存储大小，TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 存储类型          | 通用（SSD） | 通用（SSD） |
| IOPS                  | 最多 3 个 IOPS/GiB      | 最多 3 个 IOPS/GiB      |

单个超大规模 （Citus） 节点中的 RAM 总量基于所选 vCore 的数量。

| vCore 数 | 一个工作节点，GiB RAM | 协调器节点，GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

预配的存储总量还定义了每个辅助角色和协调器节点可用的 I/O 容量。

| 存储大小，TiB | 最大 IOPS |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

对于整个超量程 （Citus） 群集，聚合 IOPS 可按以下值计算：

| 辅助角色节点 | 0.5 TiB，总 IOPS | 1 TiB，总 IOPS | 2 TiB，总 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

## <a name="regions"></a>区域
超大规模 （Citus） 服务器组在以下 Azure 区域中可用：

* 美洲：
    * 加拿大中部
    * 美国中部
    * 美国东部
    * 美国东部 2
    * 美国中北部
    * 美国西部 2
* 亚太区：
    * 澳大利亚东部
    * 日本东部
    * 韩国中部
    * 东南亚
* 欧洲：
    * 北欧
    * 英国南部
    * 西欧

某些区域最初可能无法在所有 Azure 订阅上激活。 如果要使用上面列表中的区域，并且在订阅中看不到它，或者要使用此列表中未的区域，请打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="pricing"></a>定价
有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/postgresql/)。
要查看所需配置的成本[，Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)会根据您选择的选项在"**配置"** 选项卡上显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择 **"添加项目**"，展开**数据库**类别，然后选择 **"后格雷SQL" - 超缩放（Citus） 的 Azure 数据库**以自定义选项。
 
## <a name="next-steps"></a>后续步骤
了解如何[在门户中创建超大规模 （Citus） 服务器组](quickstart-create-hyperscale-portal.md)。
