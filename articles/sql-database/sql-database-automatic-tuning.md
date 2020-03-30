---
title: 自动优化概述
description: Azure SQL 数据库可分析 SQL 查询并自动适应用户工作负荷。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214099"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL 数据库中的自动优化

Azure SQL 数据库自动调优通过基于 AI 和机器学习的持续性能调优提供峰值性能和稳定工作负载。

自动优化是一项完全托管的智能性能服务，它使用内置的智能来持续监视在数据库上执行的查询，并自动提高其性能。 这是通过动态调整数据库以适应不断变化的工作负载并应用优化建议来实现的。 自动优化通过人工智能向 Azure 上的所有数据库进行横向学习，并动态地提高其优化操作。 数据库在自动调优下运行的时间越长，它的性能就越好。

Azure SQL 数据库自动调优可能是您可以启用的最重要功能之一，可用于提供稳定且性能最佳的数据库工作负荷。

## <a name="what-can-automatic-tuning-do-for-you"></a>自动调优可以为您做什么

- Azure SQL 数据库的自动性能优化
- 性能优化的自动验证
- 自动回退和自我更正
- 优化历史记录
- 优化用于手动部署的 T-SQL 脚本
- 主动的工作负载性能监视
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="safe-reliable-and-proven"></a>安全、可靠且久经考验

应用于 Azure SQL 数据库中的数据库的操作对于最紧张的工作负载的性能是完全安全的。 系统精心设计，旨在不干扰用户工作负载。 仅在利用率较低的情况下才应用自动优化建议。 系统还可暂时禁用自动优化操作以保护工作负载性能。 在此情况下，Azure 门户中会显示“由系统禁用”消息。 自动优化对工作负载持以最高的资源优先级。

自动优化机制非常成熟，已经在 Azure 上运行的数百个数据库中进行了完善。 应用的自动优化操作将会自动验证，确保工作负载性能得到显著改善。 动态检测针对退化性能的建议并立即优化。 通过优化所记录的历史记录，可清楚地跟踪对每个 Azure SQL 数据库进行的优化改进。

![自动优化的工作原理](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL 数据库自动调优正在与 SQL Server 自动调优引擎共享其核心逻辑。 有关内置智能机制的其他技术信息，请参阅 [SQL Server 自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。

若要了解自动优化工作原理的概述以及典型使用方案，请观看嵌入视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>启用自动优化

您可以在 Azure 门户中或使用[ALTER 数据库](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)T-SQL 语句[对单个数据库和池数据库启用自动调优](sql-database-automatic-tuning-enable.md)。 使用[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL 语句为托管实例部署中的实例数据库启用自动调优。

## <a name="automatic-tuning-options"></a>自动优化选项

Azure SQL 数据库中可用的自动优化选项包括：

| 自动优化选项 | 单一数据库和共用数据库支持 | 实例数据库支持 |
| :----------------------------- | ----- | ----- |
| **创建索引**- 标识可能提高工作负荷性能、创建索引并自动验证查询性能是否提高的索引。 | 是 | 否 |
| **DROP INDEX** - 每天标识冗余和重复索引，唯一索引除外，以及长时间未使用的索引（>90 天）。 请注意，此选项与使用分区切换和索引提示的应用程序不兼容。 高级和业务关键服务层不支持删除未使用的索引。 | 是 | 否 |
| **强制执行上一卓越计划**（自动更正计划）- 标识使用执行计划的 SQL 查询（该执行计划速度慢于上一卓越计划），并标识使用上一已知卓越计划的查询而不是回归计划。 | 是 | 是 |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>单个数据库和池数据库的自动调优

单个数据库和池数据库的自动调优使用**CREATE INDEX、DROP** **INDEX**和 FORCE LAST GOOD**计划数据库**顾问建议来优化数据库性能。 有关详细信息，请参阅 Azure 门户[、PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)和[REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)中的[数据库顾问建议](sql-database-advisor-portal.md)。

您可以使用 Azure 门户手动应用调优建议，也可以允许自动调优自动为您应用调优建议。 让系统自主为你应用优化建议的好处是，它会自动验证对工作负荷性能是否有正向提升，如果检测不到显著的性能改进，它会自动还原优化建议。 请注意，按照设计，如果受优化建议影响的查询不是频繁执行，则验证阶段可能要花费长达 72 小时。

如果通过 T-SQL 应用优化建议，则自动性能验证和反转机制不可用。 以这种方式应用的建议将保持活动状态，并在 24-48 小时显示在优化建议列表中。 将在 24-48 小时内保持活动状态并显示在建议列表中。 如果你想要更快地删除建议，可以通过 Azure 门户放弃它。

每个数据库都可以独立启用或禁用自动优化选项，也可以在 SQL 数据库服务器上配置这些选项，并将其应用于从服务器继承设置的每个数据库。 SQL 数据库服务器可以继承 Azure 默认值以进行自动调优设置。 目前 Azure 默认值设为启用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，禁用 DROP_INDEX。

> [!IMPORTANT]
> 从 3 月起，2020 年对 Azure 默认值的自动调优更改将生效，如下所示：
>
> - 将FORCE_LAST_GOOD_PLAN启用、禁用CREATE_INDEX和禁用DROP_INDEX \ 禁用新的 Azure 默认值。
> - 未配置自动调优首选项的现有服务器将自动配置为继承新的 Azure 默认值。 这适用于当前具有服务器设置以在未定义状态进行自动调优的所有客户。
> - 创建的新服务器将自动配置为 INHERIT 新的 Azure 默认值（与之前新服务器创建时自动调优配置处于未定义状态不同）。

在服务器上配置自动调优选项和继承属于父服务器的数据库的设置是配置自动调优的推荐方法，因为它简化了大量数据库自动调优选项的管理。

要了解如何为自动调优建议构建电子邮件通知，请参阅[自动调优的电子邮件通知](sql-database-automatic-tuning-email-notifications.md)。

### <a name="automatic-tuning-for-instance-databases"></a>实例数据库的自动调优

托管实例部署中实例数据库的自动调优仅支持**FORCE LAST GOOD 计划**。 有关通过 T-SQL 配置自动调优选项的详细信息，请参阅[自动调优引入了自动计划更正](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)和[自动计划更正](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)。

## <a name="next-steps"></a>后续步骤

- 要了解自动调优中使用的内置智能，请参阅[人工智能调调 Azure SQL 数据库](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)。
