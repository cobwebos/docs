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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214099"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL 数据库中的自动优化

Azure SQL 数据库自动优化通过基于 AI 和机器学习的持续性能优化提供高峰性能和稳定工作负荷。

自动优化是一项完全托管的智能性能服务，它使用内置的智能来持续监视在数据库上执行的查询，并自动提高其性能。 这是通过动态调整数据库以适应不断变化的工作负载并应用优化建议来实现的。 自动优化通过人工智能向 Azure 上的所有数据库进行横向学习，并动态地提高其优化操作。 数据库运行的时间越长，自动优化就会更好地执行。

Azure SQL 数据库自动优化可能是最重要的功能之一，你可以使用这些功能来提供稳定的、高性能的数据库工作负荷。

## <a name="what-can-automatic-tuning-do-for-you"></a>自动优化可为你执行的操作

- Azure SQL 数据库的自动性能优化
- 性能优化的自动验证
- 自动回退和自我更正
- 优化历史记录
- 优化用于手动部署的 T-SQL 脚本
- 主动的工作负载性能监视
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="safe-reliable-and-proven"></a>安全、可靠且久经考验

适用于 Azure SQL 数据库中的数据库的优化操作对于最重要的工作负荷的性能是完全安全的。 系统精心设计，旨在不干扰用户工作负载。 仅在利用率较低的情况下才应用自动优化建议。 系统还可暂时禁用自动优化操作以保护工作负载性能。 在这种情况下，"系统禁用" 消息将显示在 Azure 门户中。 自动优化对工作负载持以最高的资源优先级。

自动优化机制非常成熟，已经在 Azure 上运行的数百个数据库中进行了完善。 应用的自动优化操作将会自动验证，确保工作负载性能得到显著改善。 动态检测针对退化性能的建议并立即优化。 通过优化所记录的历史记录，可清楚地跟踪对每个 Azure SQL 数据库进行的优化改进。

![自动优化的工作原理](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL 数据库自动优化与 SQL Server 自动优化引擎共享其核心逻辑。 有关内置智能机制的其他技术信息，请参阅 [SQL Server 自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。

若要了解自动优化工作原理的概述以及典型使用方案，请观看嵌入视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>启用自动优化

可以在 Azure 门户中或使用[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) t-sql 语句[为单数据库和池中的数据库启用自动优化](sql-database-automatic-tuning-enable.md)。 使用[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) t-sql 语句为托管实例部署中的实例数据库启用自动优化。

## <a name="automatic-tuning-options"></a>自动优化选项

Azure SQL 数据库中可用的自动优化选项包括：

| 自动优化选项 | 单一数据库和共用数据库支持 | 实例数据库支持 |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** -标识可提高工作负荷性能的索引，创建索引，并自动验证查询的性能是否已改进。 | 是 | 否 |
| **DROP INDEX** -每天标识冗余索引和重复索引（唯一索引除外）以及长时间未使用的索引（> 90 天）。 请注意，此选项与使用分区切换和索引提示的应用程序不兼容。 高级和业务关键服务层不支持删除未使用的索引。 | 是 | 否 |
| **强制执行最后一个良好计划**（自动计划更正）-使用比上一个良好计划慢的执行计划标识 SQL 查询，并使用最近一次已知良好的计划而不是回归计划进行查询。 | 是 | 是 |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>自动优化单数据库和入池数据库

针对单个和共用数据库的自动优化使用**CREATE INDEX**、 **DROP INDEX**，并**强制执行最后一个良好的计划**数据库顾问建议，以优化数据库性能。 有关详细信息，请参阅 Azure 门户、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)和[REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)中[的数据库顾问建议](sql-database-advisor-portal.md)。

你可以使用 Azure 门户手动应用优化建议，也可以让自动优化自行应用优化建议。 让系统自主为你应用优化建议的好处是，它会自动验证对工作负荷性能是否有正向提升，如果检测不到显著的性能改进，它会自动还原优化建议。 请注意，按照设计，如果受优化建议影响的查询不是频繁执行，则验证阶段可能要花费长达 72 小时。

如果是通过 T-sql 应用优化建议，则自动性能验证和反向机制不可用。 以这种方式应用的建议将保持活动状态，并在24-48 小时的优化建议列表中显示。 系统自动提取它们之前。 如果要更快地删除建议，可以将其从 Azure 门户中丢弃。

每个数据库都可以独立启用或禁用自动优化选项，也可以在 SQL 数据库服务器上配置这些选项，并将其应用于从服务器继承设置的每个数据库。 SQL 数据库服务器可以继承自动优化设置的 Azure 默认值。 目前 Azure 默认值设为启用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，禁用 DROP_INDEX。

> [!IMPORTANT]
> 从3月起，自动优化的 Azure 默认值更改将生效2020，如下所示：
>
> - 新的 Azure 默认值将 FORCE_LAST_GOOD_PLAN 为 "已启用"、"CREATE_INDEX"、"禁用" 和 "DROP_INDEX = 已禁用"。
> - 不会将配置为自动优化首选项的现有服务器自动配置为继承新的 Azure 默认值。 这适用于当前具有未定义状态下的自动优化的服务器设置的所有客户。
> - 创建的新服务器将自动配置为继承新的 Azure 默认值（与先前版本不同的是，在创建新服务器时，自动优化配置处于未定义状态）。

如果在服务器上配置自动优化选项并继承属于父服务器的数据库的设置，则建议使用此方法配置自动优化，因为这样可以简化大量数据库的自动优化选项的管理。

若要了解有关生成自动优化建议的电子邮件通知，请参阅[自动优化电子邮件通知](sql-database-automatic-tuning-email-notifications.md)。

### <a name="automatic-tuning-for-instance-databases"></a>实例数据库的自动优化

自动优化托管实例部署中的实例数据库仅支持**强制上一项良好计划**。 有关通过 T-sql 配置自动优化选项的详细信息，请参阅[自动优化引入自动计划更正](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)和[自动计划更正](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)。

## <a name="next-steps"></a>后续步骤

- 若要了解自动优化中使用的内置智能，请参阅[人工智能优化 AZURE SQL 数据库](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)。
