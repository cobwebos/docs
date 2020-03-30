---
title: 性能选项 • 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 超大规模（Citus）服务器组的选项，包括节点计算、存储和区域。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462405"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>用于后格雷SQL的 Azure 数据库 – 超大规模（Citus） 性能选项

## <a name="compute-and-storage"></a>计算和存储
 
您可以为超大规模 （Citus） 服务器组中的工作节点和协调器节点单独选择计算和存储设置。  计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 用于预配的存储大小是指超大规模 （Citus） 服务器组中的协调员和辅助节点可用的容量。 存储包括数据库文件、临时文件、事务日志和 Postgres 服务器日志。 预配的存储总量还定义了每个辅助角色和协调器节点可用的 I/O 容量。
 
|                       | 工作器节点           | 协调员节点      |
|-----------------------|-----------------------|-----------------------|
| 计算，vCore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| 每个 vCore 的内存，GiB | 8                     | 4                     |
| 存储大小，TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 存储类型          | 通用（SSD） | 通用（SSD） |
| IOPS                  | 最多 3 个 IOPS/GiB      | 最多 3 个 IOPS/GiB      |


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
