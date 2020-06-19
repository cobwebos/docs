---
title: 工作负荷管理
description: 关于在 Azure Synapse Analytics 中实施工作负荷管理的指南。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 23ede806b627ad0f77e325ab391d37347f4bb29f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650449"
---
# <a name="what-is-workload-management"></a>什么是工作负荷管理？

运行混合工作负荷会给繁忙的系统带来资源挑战。  解决方案架构师会设法分离经典数据仓库活动（例如加载、转换和查询数据），以确保存在足够的资源来满足 SLA。  

物理服务器隔离可能导致基础结构的某些部分未充分利用、过度预定或处于因启动和停止硬件而不断准备缓存的状态。  成功的工作负荷管理方案有效地管理资源，确保资源利用率非常高效，并最大化投资回报率 (ROI)。

数据仓库工作负荷指围绕数据仓库而发生的所有操作。 这些组件的深度和广度取决于数据仓库的成熟度。  数据仓库工作负荷包括：

- 将数据加载到仓库的整个过程
- 执行数据仓库分析和报告
- 管理数据仓库中的数据
- 从数据仓库导出数据

数据仓库的性能容量由[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)决定。

- 若要查看为所有性能配置文件分配的资源，请参阅[内存和并发限制](memory-concurrency-limits.md)。
- 若要调整容量，可以[纵向扩展或缩减](quickstart-scale-compute-portal.md)。

## <a name="workload-management-concepts"></a>工作负荷管理概念

过去，对于 Azure Synapse 中的 Synapse SQL，要通过[资源类](resource-classes-for-workload-management.md)管理查询性能。  允许基于角色成员身份将内存分配给查询的资源类。  与资源类相关的主要挑战是，一旦配置后，就不能管理或控制工作负荷。  

例如，如果将临时用户角色成员身份授予 smallrc，则允许该用户使用系统上 100% 的内存。  对于资源类，无法预留资源并确保其可用于关键工作负荷。

Azure Synapse 中的 Synapse SQL 池工作负荷管理包括三个高级概念：[工作负荷分类](sql-data-warehouse-workload-classification.md)、[工作负荷重要性](sql-data-warehouse-workload-importance.md)和[工作负荷隔离](sql-data-warehouse-workload-isolation.md)。  通过这些功能，可以更好地控制工作负荷如何使用系统资源。

工作负荷分类的概念是，将请求分配到工作负荷组并设置重要性级别。  过去，这种分配通过使用 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class) 的角色成员身份完成。  现在可通过[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)完成此操作。  分类功能提供更丰富的一组选项，例如标签、会话和对请求进行分类的时间。

工作负荷重要性影响请求获取资源访问权限的顺序。  在繁忙的系统上，重要性较高的请求具有优先访问资源的权限。  重要性还可以确保按顺序访问锁定。

工作负荷隔离为工作负荷组预留资源。  工作负荷组中保留的资源专门为该工作负荷组保留，以确保执行。  工作负荷组还可用于定义为每个请求分配的资源量，与资源类很像。  通过工作负荷组，可以保留或限制一组请求可使用的资源量。  最后，工作负荷组是一种机制，用于将查询超时等规则应用到请求。  

## <a name="next-steps"></a>后续步骤

- 有关工作负荷分类的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)。  
- 有关工作负荷隔离的详细信息，请参阅[工作负荷隔离](sql-data-warehouse-workload-isolation.md)。  
- 有关工作负荷重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)。  
- 有关工作负荷管理监视的详细信息，请参阅[工作负荷管理门户监视](sql-data-warehouse-workload-management-portal-monitor.md)。  
