---
title: 工作负荷管理
description: 在 Azure Synapse Analytics 中实施工作负荷管理的指南。
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
ms.openlocfilehash: dd867d4aa9a9ef5ed73e78a46826a8cd5239039b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80744232"
---
# <a name="what-is-workload-management"></a>什么是工作负荷管理？

运行混合工作负荷可能会在繁忙的系统上造成资源压力。  解决方案架构师寻求隔离经典数据仓库活动（例如加载、转换和查询数据）的方法，以确保有足够的资源来满足 SLA。  

物理服务器隔离可能会导致基础设施的容量利用不足、过度预留，或者进入一种随着硬件启动和停止而不断装填缓存的状态。  成功的工作负荷管理方案能够有效地管理资源，确保高效的资源利用，并将投资回报率 (ROI) 最大化。

数据仓库工作负荷是指发生的与数据仓库相关的所有操作。 这些组件的深度与广度取决于数据仓库的成熟度。  数据仓库工作负荷包括：

- 将数据载入仓库的整个过程
- 执行数据仓库分析和报告
- 管理数据仓库中的数据
- 从管理数据仓库导出数据

数据仓库的性能容量由[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)决定。

- 若要查看为所有性能配置文件分配的资源，请参阅[内存和并发限制](memory-concurrency-limits.md)。
- 若要调整容量，可以[纵向扩展或缩减](quickstart-scale-compute-portal.md)。

## <a name="workload-management-concepts"></a>工作负荷管理的概念

过去，对于 Azure Synapse 中的 SQL Analytics，你通过[资源类](resource-classes-for-workload-management.md)管理了查询性能。  使用资源类可以根据角色成员身份将内存分配给查询。  使用资源类的主要难题在于，一旦配置，就会缺少监管，或者无法控制工作负荷。  

例如，如果将一个临时用户角色成员身份授予 smallrc，则允许该用户使用系统上的所有内存。  使用资源类时无法预留资源，也无法确保关键工作负荷有可用的资源。

Azure Synapse 中的 Synapse SQL 池工作负荷管理包括三个高级概念：[工作负荷分类](sql-data-warehouse-workload-classification.md)、[工作负荷重要性](sql-data-warehouse-workload-importance.md)和[工作负荷隔离](sql-data-warehouse-workload-isolation.md)。  这些功能可让你更好地控制工作负荷如何利用系统资源。

工作负荷分类的概念是指将请求分配到工作负荷组并设置重要性级别。  过去，这种分配是使用 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class) 通过角色成员身份实现的。  现在可以通过[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)来实现。  分类功能提供更丰富的一组选项（例如标签、会话和时间）来对请求进行分类。

工作负荷重要性影响请求获取资源访问权限的顺序。  在繁忙的系统上，重要性较高的请求可以先访问资源。  重要性还可确保按顺序访问锁。

工作负荷隔离为工作负荷组预留资源。  工作负荷组中预留的资源专供该工作负荷组使用，以确保能够执行它。  工作负荷组还允许定义分配给每个请求的资源量，这与资源类的功能非常类似。  使用工作负荷组可以预留一组请求可以消耗的资源量，或者限制其资源量。  最后，工作负荷组是对请求应用规则（例如查询超时）的机制。  

## <a name="next-steps"></a>后续步骤

- 有关工作负荷分类的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)。  
- 有关工作负荷隔离的详细信息，请参阅[工作负荷隔离](sql-data-warehouse-workload-isolation.md)。  
- 有关工作负荷重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)。  
- 有关工作负荷管理监视的详细信息，请参阅[工作负荷管理门户监视](sql-data-warehouse-workload-management-portal-monitor.md)。  
