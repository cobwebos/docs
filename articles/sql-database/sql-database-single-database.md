---
title: 什么是 Azure SQL 数据库单一数据库 | Microsoft Docs
description: 了解 Azure SQL 数据库中的单一数据库
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: c5de4277915e71763f034596d372554e4f88e204
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316658"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>什么是 Azure SQL 数据库中的单一数据库

单一数据库部署选项使用自己的资源集在 Azure SQL 数据库中创建数据库，并通过 SQL 数据库服务器进行管理。 使用单一数据库，每个数据库都彼此独立且可移植，各自在[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)或[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)中有其自己的服务层级，并具备有保证的计算大小。

> [!IMPORTANT]
> 单一数据库是用于 Azure SQL 数据库的三个部署选项之一。 其他两个是[弹性池](sql-database-elastic-pool.md)和[托管实例](sql-database-managed-instance.md)。
> [!NOTE]
> 有关 Azure SQL 数据库术语词汇表，请参阅 [SQL 数据库术语词汇表](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>动态可伸缩性

可以在无服务器（预览版）计算层级中以低成本构建第一个基于小型单一数据库的应用，或者在预配的计算层级中构建小型计算大小。 可以根据解决方案的需要，随时以手动或编程方式更改[计算或服务层级](sql-database-single-database-scale.md)。 可在不给应用或客户造成停机的情况下调整性能。 动态可伸缩性可让数据库以透明方式响应快速变化的资源要求，使用户只需为用到的资源付费。

## <a name="single-databases-and-elastic-pools"></a>单一数据库和弹性池

单一数据库可以移进或移出[弹性池](sql-database-elastic-pool.md)以进行资源共享。 许多业务和应用程序只要能够创建单一数据库并按需调高或调低性能即可，尤其是当使用模式相对容易预测时。 但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。 弹性池旨在解决此问题。 概念很简单。 可以向池而不是单个数据库分配性能资源，并且仅需为池的总体性能资源付费，而无需为单一数据库的性能付费。

## <a name="monitoring-and-alerting"></a>监视和警报

将内置的[性能监视](sql-database-performance.md)和[警报工具](sql-database-insights-alerts-portal.md)与性能等级组合使用。 使用这些工具，可以根据当前需求或项目性能的需求，快速评估调高或调低性能产生的影响。 此外，SQL 数据库可[发出指标和诊断日志](sql-database-metrics-diag-logging.md)，以便更轻松地监视。

## <a name="availability-capabilities"></a>可用性功能

单一数据库、弹性池和托管实例全都提供许多可用性特征。 有关信息，请参阅[可用性特征](sql-database-technical-overview.md#availability-capabilities)。

## <a name="transact-sql-differences"></a>Transact-SQL 的差异

Microsoft SQL Server 和 Azure SQL 数据库都完全支持应用程序使用的大多数 Transact-SQL 功能。 例如，核心 SQL 组件（如数据类型、运算符、字符串、算术、逻辑和光标函数等）在 SQL Server 和 SQL 数据库中的工作方式相同。 但是，DDL（数据定义语言）和 DML（数据操作语言）元素中的一些 T-SQL 差异导致存在仅部分受支持的 T-SQL 语句和查询（我们会在本文后面的内容中介绍）。
此外，还有一些功能和语法根本不受支持，因为 Azure SQL 数据库旨在将功能与 master 数据库和操作系统的依赖项隔离。 因此，大多数服务器级活动不适用于 SQL 数据库。 T-SQL 语句和选项在配置服务器级选项、操作系统组件或指定文件系统配置时不可用。 需要此类功能时，通常是以某种其他方式从 SQL 数据库或从其他 Azure 功能或服务获取相应的替代项。

有关详细信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](sql-database-transact-sql-information.md)。

## <a name="security"></a>安全性

SQL 数据库提供一系列[内置安全性和符合性](sql-database-security-overview.md)功能，帮助应用程序满足各种安全性和符合性要求。

> [!IMPORTANT]
> Azure SQL 数据库（所有部署选项）已通过了许多合规性标准的认证。 有关详细信息, 请参阅[Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), 你可以在其中找到最新的 SQL 数据库符合性认证列表。

## <a name="next-steps"></a>后续步骤

- 若要快速开始使用单个数据库，请首先了解[单一数据库快速入门指南](sql-database-single-database-quickstart-guide.md)。
- 若要详细了解如何将 SQL Server 数据库迁移到 Azure，请参阅[迁移到 Azure SQL 数据库](sql-database-single-database-migrate.md)。
- 有关支持的功能的信息，请参阅[功能](sql-database-features.md)。
