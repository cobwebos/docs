---
title: 工作负荷重要性 |Microsoft Docs
description: 有关设置 Azure SQL 数据仓库中查询的重要性的指南。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: e53a6fcefb0f5370f6e24cc50fad2ad4ad4c64e3
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57876215"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>SQL 数据仓库工作负荷重要性 （预览版）

本文介绍了工作负荷重要性可以如何影响 SQL 数据仓库的请求的执行顺序。

> [!Note]
> SQL 数据仓库 Gen2 上提供了工作负荷的重要性。

## <a name="importance"></a>Importance

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

业务需求可能需要数据仓库工作负荷要比其他更重要。  假设之前会计期间关闭任务关键的销售数据的加载位置。  例如天气数据不具有严格的 Sla，对于其他源加载数据。   加载销售数据的请求的重要性高和低重要性设置为加载是否数据确保销售数据负载的请求获取对资源的第一次访问，并更快速地完成。

## <a name="importance-levels"></a>重要性级别

有五个级别的重要性： 低、 below_normal、 normal、 above_normal 和高。  不设置重要性的请求都分配默认级别的正常。  具有相同的重要性级别的请求必须现在存在的调度行为相同。

## <a name="importance-scenarios"></a>重要性方案

与销售额和天气数据上面所述的基本重要性方案，以外还有其他工作负荷重要性帮助满足数据处理和查询需求的方案。

### <a name="locking"></a>锁定

访问到锁进行读取和写入活动是自然争用的一个领域。  如活动[分区切换](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition)或[重命名对象](/sql/t-sql/statements/rename-transact-sql)需要提升的锁。  而无需工作负荷的重要性，SQL 数据仓库优化吞吐量。  优化吞吐量意味着，运行时和排队的请求具有相同的锁定需求并提供了资源，排队的请求可以绕过之前到达请求队列中具有更高版本需要锁定请求。  一旦工作负荷重要性应用于具有更高版本的锁定请求都需要。 使用重要性较低，将请求之前运行较高的优先级与请求。

下面是一个示例：

第 1 季度是主动运行，并选择销售数据中的数据。
第 2 季度将会排队等待第 1 季度才能完成。  它在上午 9 点已提交，并试图对新数据进行分区切换到销售数据。
第 3 季度上午 9:01 提交，并且想要从销售数据中选择数据。

如果第 2 季度和年第 3 季度具有相同的重要性，并且仍在执行第 1 季度，第 3 季度将开始执行。 第 2 季度将继续等待销售数据上的排他锁。  如果第 2 季度具有较高的优先级比第 3 季度，它可以开始执行之前完成第 2 季度之前将等待第 3 季度。

### <a name="non-uniform-requests"></a>非统一请求

其中重要性可以帮助满足查询需求的另一种情况是当提交具有不同的资源类的请求。  如先前所述，在相同的重要性，SQL 数据仓库优化吞吐量。  时 （例如 smallrc 或 mediumrc） 的混合的大小请求排队，SQL 数据仓库将选择最早到达范围内的可用资源的请求。  如果应用工作负荷的重要性下, 一步计划的最高的重要性请求。
  
请考虑在 DW500c 下面的示例：

第 1 季度、 第 2 季度、 年，第 3 季度和第 4 季度运行 smallrc 查询。
问题 5 是在上午 9 点提交 mediumrc 资源类。
问题 6 是上午 9:01 提交 smallrc 资源类。

问题 5 是 mediumrc，因为它需要两个并发槽。  问题 5 需要等待两个正在运行的查询执行完毕。  但是，一个正在运行的查询 （第 1 季度-第 4 季度） 完成时，问题 6 计划立即因为资源来执行该查询存在。  如果问题 5 具有较高的优先级比问题 6，问题 6 等待，直到问题 5 运行之前它可在开始执行。

## <a name="next-steps"></a>后续步骤

有关 SQL 数据仓库工作负荷分类的详细信息，请参阅[SQL 数据仓库工作负荷分类](sql-data-warehouse-workload-classification.md)并[创建工作负荷分类器](quickstart-create-a-workload-classifier-tsql.md)。 请参阅[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)若要查看查询和分配的重要性。
