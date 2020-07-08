---
title: 缩放资源
description: 本文介绍如何通过添加或删除已分配资源，在 Azure SQL 数据库和 SQL 托管实例中缩放数据库。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 180fca9ae40bc07be762665a3d16270e905d2e02
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984118"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>以最短的停机时间动态缩放数据库资源
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

使用 Azure SQL 数据库和 SQL 托管实例可以将更多资源动态添加到数据库中，使[停机时间](https://azure.microsoft.com/support/legal/sla/sql-database)最短;但是，在一段较短的时间内，会有一次与数据库的连接丢失的情况，可以使用重试逻辑来缓解这种情况。

## <a name="overview"></a>概述

当你的应用程序的需求从数个设备和客户增加到数百万个时，Azure SQL 数据库和 SQL 托管实例会动态缩小，且停机时间最短。 可伸缩性是平台即服务（PaaS）中最重要的特征之一，可让你在需要时将更多资源动态添加到服务。 Azure SQL 数据库允许轻松更改分配给数据库的资源（CPU 电源、内存、IO 吞吐量和存储）。

可缓解由于应用程序的使用情况增加而导致的使用索引或查询重写方法无法修复的性能问题。 添加更多资源可在数据库达到当前资源限制并需要更多电源处理传入的工作负荷时快速做出反应。 Azure SQL 数据库还可以在不需要资源以降低成本时缩减资源。

不必担心购买硬件和更改基础结构。 使用滑块可以轻松地通过 Azure 门户来缩放数据库。

![缩放数据库性能](./media/scale-resources/scale-performance.svg)

Azure SQL 数据库提供[基于 DTU 的购买模型](service-tiers-dtu.md)和[基于 vCore 的购买](service-tiers-vcore.md)模型，而 azure sql 托管实例仅提供[基于 vCore 的购买模型](service-tiers-vcore.md)。 

- [基于 DTU 的购买模型](service-tiers-dtu.md)在三个服务层中提供计算、内存和 i/o 资源的混合，以支持轻型到重型数据库工作负荷：基本、标准和高级。 每个层中的不同性能级别提供这些资源的不同组合，你可以向其添加更多的存储资源。
- [基于 vCore 的购买模型](service-tiers-vcore.md)允许选择 vCore 数、内存容量，以及存储的容量和速度。 此购买模型提供三个服务层：常规用途、业务关键和超大规模。

可以在小型单一数据库中构建第一个应用，每个月只需在“常规用途”服务层级中花费少量资金。然后可以根据解决方案的需要，随时手动或以编程方式将服务层级更改为“业务关键”服务层级。 可在不给应用或客户造成停机的情况下调整性能。 动态可伸缩性可让数据库以透明方式响应快速变化的资源要求，使用户只需为用到的资源付费。

> [!NOTE]
> 动态可伸缩性不同于自动缩放。 自动缩放是指服务根据条件自动缩放，而动态可伸缩性允许在最短停机时间的情况下进行手动缩放。

Azure SQL 数据库中的单个数据库支持手动动态缩放，但不支持自动缩放。 若要获得更多的*自动化*体验，请考虑使用弹性池，这允许数据库根据单个数据库需求共享池中的资源。
但是，有一些脚本可以帮助自动执行 Azure SQL 数据库中单一数据库的可伸缩性。 有关示例，请参阅[使用 PowerShell 监视和缩放单个 SQL 数据库](scripts/monitor-and-scale-database-powershell.md)。

可以随时更改 [DTU 服务层级](service-tiers-dtu.md)或 [vCore 特征](resource-limits-vcore-single-databases.md)，将应用程序故障时间降至最低（通常在平均 4 秒以下）。 许多业务和应用只要能够创建数据库并按需调高或调低性能即可，尤其是当使用模式相对容易预测时。 但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。 对于这种情况，可以使用一个具有一定数量 eDTU 的弹性池，这些 eDTU 在池中的多个数据库之间共享。

![SQL 数据库简介：按层和级别统计的单一数据库 DTU](./media/scale-resources/single_db_dtus.png)

Azure SQL 数据库提供动态缩放数据库的功能：

- 在[单一数据库](single-database-scale.md)中，可以使用 [DTU](resource-limits-dtu-single-databases.md) 或 [vCore](resource-limits-vcore-single-databases.md) 模型来定义将分配给每个数据库的最大资源量。
- [弹性池](elastic-pool-scale.md)允许定义池中每组数据库的最大资源限制。

还可以通过 Azure SQL 托管实例进行扩展： 

- [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)使用[vcore](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)模式，并使你可以定义分配给实例的最大 CPU 核心数和最大存储量。 托管实例中的所有数据库都将共享分配给该实例的资源。

以任何风格启动纵向扩展或缩减操作将会重启数据库引擎进程，并根据需要将其移到另一虚拟机。 将数据库引擎进程移到新虚拟机是一个**在线过程**，在该过程进行时，你可以继续使用现有的 Azure SQL 数据库服务。 目标数据库引擎完全启动并做好处理查询的准备以后，连接会[从源数据库引擎切换到目标数据库引擎](single-database-scale.md#impact)。

> [!NOTE]
> 当放大/缩小过程完成时，可能会出现短暂的连接中断。 如果已实现了[标准暂时性错误的重试逻辑](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)，则不会注意到故障转移。

## <a name="alternative-scale-methods"></a>替代缩放方法

在不更改数据库或应用程序代码的情况下，缩放资源是提升数据库性能的最简单和最有效的方法。 在某些情况下，即使是最高的服务层级、计算大小和性能优化，也可能无法以成功和经济高效的方式处理工作负载。 在该情况下，可选择其他选项对数据库进行缩放：

- [读取扩展](read-scale-out.md)是一项可用功能，您可以在其中获取数据的一个只读副本，您可以在其中执行要求严格的只读查询（如报表）。 只读副本将处理只读工作负荷，而不会影响主数据库上的资源使用。
- [数据库分片](elastic-scale-introduction.md)是一组技术，可用于将数据拆分为多个数据库，并单独对这些数据库进行缩放。

## <a name="next-steps"></a>后续步骤

- 有关通过更改数据库代码提升数据库性能的信息，请参阅[查找并应用性能建议](database-advisor-find-recommendations-portal.md)。
- 有关让内置数据库智能优化数据库的信息，请参阅[自动微调](automatic-tuning-overview.md)。
- 有关 Azure SQL 数据库中的读取横向扩展的信息，请参阅如何[使用只读副本对只读查询工作负荷进行负载均衡](read-scale-out.md)。
- 有关数据库分片的详细信息，请参阅[使用 Azure SQL 数据库进行扩展](elastic-scale-introduction.md)。
