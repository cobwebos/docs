---
title: Azure SQL 数据仓库发行说明（2018 年 8 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/13/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c52fcd544e22c274d0e9053da93d2b9dee0df695
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286626"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 8 月
Azure SQL 数据仓库持续得到改进。 本文介绍了 2018 年 8 月发行的版本中所引入的新功能和所做的更改。

## <a name="automatic-intelligent-insights"></a>自动化智能见解
Microsoft 引入了[自动化智能见解](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/)来为数据仓库在云中实现自动化功能。 你将不再需要监视数据仓库来了解数据倾斜和非最优表统计信息。 SQL 数据仓库在不增加成本的情况下为所有第 2 代实例提供了智能见解。 通过与 [Azure 顾问](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)集成，你可以自动接收最佳做法建议来提高你的活动工作负荷的性能。 SQL 数据仓库对工作负荷进行分析并基于使用情况提供建议。 此分析每天进行，使得你可以监视使用情况报告和建议，以便改进工作负荷。

可以在 Azure 顾问门户中查看建议：![适用于 Azure SQL 数据仓库的 Azure 顾问门户建议](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

可以钻取到每个类别来查看针对特定警报的建议：![适用于 Azure SQL 数据仓库的 Azure 顾问门户建议详细信息](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Bug 修复

| 标题 | Description |
|:---|:---|
| **当拆分计数超过最大限制时，潜在查询失败** |当超过文件拆分数上限（即 100 万个）时，出现未经处理的异常，它导致 SQL 引擎转储且所有查询均失败。 该修补程序解决了此问题，它正确地处理了异常并在不导致查询失败的情况下返回一个错误。 |
| **已将 ExternalMoveReadersPerNode 默认值调高以提高负载性能** |此问题是由 ExternalMoveReadersPerNode 属性设置与服务结构设置不同步引起的。 此回归导致 Gen2 负载性能降级。 该修补程序将 Gen2 负载性能恢复到了优化后的设计参数范围内。|


## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请了解如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]。 如果不熟悉 Azure，在遇到新术语时，可以参考 [Azure 术语表][Azure glossary]。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [堆栈溢出论坛]
* [Twitter]


[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md