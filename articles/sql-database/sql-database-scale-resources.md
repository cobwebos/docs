---
title: Azure SQL 数据库缩放资源 | Microsoft Docs
description: 本文介绍如何通过添加或删除已分配的资源来缩放数据库。
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: a6b987d9815cfabed6dd986a0d9842a97f5b5868
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092046"
---
# <a name="scale-database-resources"></a>缩放数据库资源

Azure SQL 数据库允许以最短的停机时间向数据库动态添加更多资源。

## <a name="overview"></a>概述

当对应用的需求从数个设备和客户增加到数百万个时，Azure SQL 数据库可动态缩放规模，并最大限度减少停机时间。 可伸缩性是 PaaS 最重要的特性之一，它允许在需要时向服务动态添加更多资源。 Azure SQL 数据库允许轻松更改分配给数据库的资源（CPU 电源、内存、IO 吞吐量和存储）。  
可缓解由于应用程序的使用情况增加而导致的使用索引或查询重写方法无法修复的性能问题。 添加更多资源可在数据库达到当前资源限制并需要更多电源处理传入的工作负荷时快速做出反应。 Azure SQL 数据库还可以在不需要资源以降低成本时缩减资源。
不必担心购买硬件和更改基础结构。 可以使用滑块通过 Azure 门户轻松缩放数据库。

![缩放数据库性能](media/sql-database-scalability/scale-performance.svg)

Azure SQL 数据库提供[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)或[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。 
-   [基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)在三个服务层中提供包括计算、内存和 IO 资源在内的各种内容，支持轻型到重型数据库工作负荷：基本、标准、高级。 每个层中的不同性能级别提供这些资源的不同组合，你可以向其添加更多的存储资源。
-   [基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)允许选择 vCore 数、内存容量，以及存储的容量和速度。
可以在小型单一数据库中构建第一个应用，每个月只需花费少量资金。然后可以根据解决方案的需要，随时手动或以编程方式更改服务层。 可在不给应用或客户造成停机的情况下调整性能。 动态可伸缩性可让数据库以透明方式响应快速变化的资源要求，使用户只需为用到的资源付费。


> [!NOTE]
> 动态可伸缩性不同于自动缩放。 自动缩放是指服务根据条件自动缩放，而动态可伸缩性允许在无停机时间的情况下进行手动缩放。
>


单个 Azure SQL 数据库支持手动动态可伸缩性，但不支持自动缩放。 若要获得更多*自动*体验，请考虑使用弹性池，它允许数据库根据各个数据库需求共享池中的资源。
但是，有一些脚本可帮助自动执行单个 Azure SQL 数据库的可伸缩性。 有关示例，请参阅[使用 PowerShell 监视和缩放单个 SQL 数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)。


所有三种 Azure SQL 数据库都提供一些动态缩放数据库的功能：
-   在 [Azure SQL 单一数据库](sql-database-single-database-scale.md)中，可以使用 [DTU](sql-database-dtu-resource-limits-single-databases.md) 或 [vCore](sql-database-vcore-resource-limits-single-databases.md) 模型来定义将分配给每个数据库的最大资源量。
-   [Azure SQL 托管实例](sql-database-managed-instance.md)使用 [vCores](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) 模式，并允许定义分配给实例的最大 CPU 核心数和最大存储空间。 该实例中的所有数据库都将共享分配给该实例的资源。
-   [Azure SQL 弹性池](sql-database-elastic-pool-scale.md)允许定义池中每组数据库的最大资源限制。

## <a name="alternative-scale-methods"></a>替代缩放方法
在不更改数据库或应用程序代码的情况下，缩放资源是提升数据库性能的最简单和最有效的方法。
在某些情况下，即使是最高的性能层和性能优化，也可能无法以成功和经济高效的方式处理工作负荷。 在该情况下，可选择其他选项对数据库进行缩放：
-   [读取扩展](sql-database-read-scale-out.md)是一项在获取数据的一个只读副本时可用的功能，可在该副本中执行要求的只读查询，如报表。 只读副本将处理只读工作负荷，而不会影响主数据库上的资源使用情况。
-   [数据库分片](sql-database-elastic-scale-introduction.md)是一组技术，可用于将数据拆分为多个数据库，并单独对这些数据库进行缩放。

## <a name="next-steps"></a>后续步骤
- 有关通过更改数据库代码提升数据库性能的信息，请参阅[查找并应用性能建议](sql-database-advisor-portal.md)。
- 有关让内置数据库智能优化数据库的信息，请参阅[自动微调](sql-database-automatic-tuning.md)。
- 有关 Azure SQL 数据库服务中读取扩展的信息，请参阅如何[使用只读副本实现只读查询工作负荷负载均衡](sql-database-read-scale-out.md)。
- 有关数据库分片的详细信息，请参阅[使用 Azure SQL 数据库进行扩展](sql-database-elastic-scale-introduction.md)。

