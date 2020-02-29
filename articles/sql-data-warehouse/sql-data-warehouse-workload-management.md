---
title: 工作负荷管理
description: 在 Azure Synapse Analytics 中实施工作负荷管理的指南。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14ea742a40afff8105560f1003655004687c7c9e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197651"
---
# <a name="what-is-workload-management"></a>什么是工作负荷管理？

运行混合工作负荷会给繁忙的系统带来资源难题。  解决方案架构师会寻找用于分离经典数据仓库活动（如加载、转换和查询数据）的方法，以确保存在足够的资源来满足 Sla 要求。  

物理服务器隔离可能会导致口袋的基础结构未充分利用、overbooked 或处于状态，其中缓存在硬件开始和停止后会不断受到高速。  成功的工作负荷管理方案有效地管理资源，确保资源利用率非常高效，并最大化投资回报率（ROI）。

数据仓库工作负荷是指与数据仓库相关的所有操作。 这些组件的深度和广度取决于数据仓库的成熟度。  数据仓库工作负荷包括： 
- 将数据加载到仓库的整个过程 
- 执行数据仓库分析和报告
- 管理数据仓库中的数据 
- 从数据仓库导出数据

数据仓库的性能容量由[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)决定。
- 若要查看为所有性能配置文件分配的资源，请参阅[内存和并发限制](memory-concurrency-limits.md)。
- 若要调整容量，可以[增加或减少](quickstart-scale-compute-portal.md)。


## <a name="workload-management-concepts"></a>工作负荷管理概念
过去，对于 Azure Synapse 中的 SQL Analytics，你通过[资源类](resource-classes-for-workload-management.md)管理了查询性能。  允许将内存分配给基于角色成员身份的查询的资源类。  资源类的主要挑战是，一旦配置后，就不会控制工作负荷。  

例如，如果将即席用户角色成员资格授予 smallrc，则允许该用户使用系统上的100% 内存。  对于资源类，无法预留和确保资源可用于关键工作负荷。

Azure Synapse 中的 SQL Analytics 工作负荷管理包括三个高级概念：[工作负荷分类](sql-data-warehouse-workload-classification.md)、[工作负荷重要性](sql-data-warehouse-workload-importance.md)和[工作负荷隔离](sql-data-warehouse-workload-isolation.md)。  这些功能使你可以更好地控制工作负荷使用系统资源的方式。

工作负荷分类是将请求分配到工作负荷组并设置重要性级别的概念。  过去，此分配通过使用[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)的角色成员身份完成。  现在可以通过[CREATE 工作负荷 CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)完成此操作。  分类功能提供了更丰富的选项，如标签、会话和对请求进行分类的时间。

工作负荷重要性影响请求获取资源访问权限的顺序。  在繁忙的系统上，具有较高重要性的请求首先具有对资源的访问权限。  重要性还可以确保按顺序访问锁定。 

工作负荷隔离为工作负荷组预留资源。  工作负荷组中保留的资源专门为该工作负荷组保留，以确保执行。  工作负荷组还允许您定义每个请求分配的资源量，与资源类的操作非常类似。  工作负荷组使你能够保留或上限一组请求可使用的资源量。  最后，工作负荷组是一种机制，用于将查询超时等规则应用到请求。  


## <a name="next-steps"></a>后续步骤

- 有关工作负荷分类的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)。  
- 有关工作负荷隔离的详细信息，请参阅[工作负荷隔离](sql-data-warehouse-workload-isolation.md)。  
- 有关工作负荷重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)。  
- 有关工作负荷管理监视的详细信息，请参阅[工作负荷管理门户监视](sql-data-warehouse-workload-management-portal-monitor.md)。  
