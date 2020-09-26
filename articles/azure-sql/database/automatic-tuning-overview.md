---
title: 自动优化概述
description: Azure SQL 数据库和 Azure SQL 托管实例可分析 SQL 查询并自动适应用户工作负载。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 03/30/2020
ms.openlocfilehash: 4c2faa6f015a8c1ce8f360155abdc14367d3057b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330734"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL 数据库与 Azure SQL 托管实例中的自动优化
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

通过基于人工智能和机器学习的持续性能优化，Azure SQL 数据库和 Azure SQL 托管实例自动优化可以提供最佳性能和稳定的工作负载。

自动优化是一项完全托管的智能性能服务，它使用内置的智能来持续监视在数据库上执行的查询，并自动提高其性能。 这是通过动态调整数据库以适应不断变化的工作负载并应用优化建议来实现的。 自动优化通过人工智能向 Azure 上的所有数据库进行横向学习，并动态地提高其优化操作。 在开启自动优化时，数据库运行时间越长，执行能力就越好。

Azure SQL 数据库和 Azure SQL 托管实例自动优化可能是可用于提供稳定及最佳性能数据库工作负载的最重要的功能之一。

## <a name="what-can-automatic-tuning-do-for-you"></a>自动优化有何作用

- 数据库的自动性能优化
- 性能优化的自动验证
- 自动回退和自我更正
- 优化历史记录
- 优化用于手动部署的 Transact-SQL (T-SQL) 脚本
- 主动的工作负载性能监视
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="safe-reliable-and-proven"></a>安全、可靠且久经考验

应用于 Azure SQL 数据库中数据库的优化操作非常安全，可满足最紧张工作负载的性能需求。 系统精心设计，旨在不干扰用户工作负载。 仅在利用率较低的情况下才应用自动优化建议。 系统还可暂时禁用自动优化操作以保护工作负载性能。 在此情况下，Azure 门户中会显示“由系统禁用”消息。 自动优化对工作负载持以最高的资源优先级。

自动优化机制非常成熟，已经在 Azure 上运行的数百个数据库中进行了完善。 应用的自动优化操作将会自动验证，确保工作负载性能得到显著改善。 动态检测针对退化性能的建议并立即优化。 通过优化所记录的历史记录，可清楚地跟踪对 Azure SQL 数据库和 Azure SQL 托管实例中的每个数据库进行的优化改进。

![自动优化的工作原理](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

Azure SQL 数据库自动优化与数据库引擎中的 SQL Server 自动优化功能共享其核心逻辑。 有关内置智能机制的其他技术信息，请参阅 [SQL Server 自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。

若要了解自动优化工作原理的概述以及典型使用方案，请观看嵌入视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>启用自动优化

- [在 Azure 门户中为 Azure SQL 数据库启用自动优化](automatic-tuning-enable.md)或使用 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL 语句来启用。
- 通过使用 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL 语句为 Azure SQL 托管实例启用自动优化。

## <a name="automatic-tuning-options"></a>自动优化选项

Azure SQL 数据库和 Azure SQL 托管实例中可用的自动优化选项包括：

| 自动优化选项 | 单一数据库和共用数据库支持 | 实例数据库支持 |
| :----------------------------- | ----- | ----- |
| **创建索引** - 标识可提高工作负载性能的索引，创建索引，并自动验证查询性能是否有所提高。 | 是 | 否 |
| **删除索引** - 每日识别冗余和重复的索引，但不包括唯一索引和长时间（>90 天）未使用的索引。 请注意，此选项与使用分区切换和索引提示的应用程序不兼容。 高级和业务关键服务层不支持删除未使用的索引。 | 是 | 否 |
| **强制执行上一卓越计划**（自动更正计划）- 标识使用执行计划的 Azure SQL 查询（该执行计划速度慢于上一卓越计划），并标识使用上一已知卓越计划的查询而不是回归计划。 | 是 | 是 |

### <a name="automatic-tuning-for-sql-database"></a>SQL 数据库的自动优化

Azure SQL 数据库的自动优化使用“创建索引”、“删除索引”和“强制执行上一个卓越计划”数据库顾问建议来优化你的数据库性能  。 有关详细信息，请参阅 [Azure 门户中的](database-advisor-find-recommendations-portal.md)、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) 中的和 [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning) 中的数据库顾问建议。

可以使用 Azure 门户手动应用优化建议，也可以让“自动优化”自主为你应用优化建议。 让系统自主为你应用优化建议的好处是，它会自动验证对工作负荷性能是否有正向提升，如果检测不到显著的性能改进，它会自动还原优化建议。 请注意，按照设计，如果受优化建议影响的查询不是频繁执行，则验证阶段可能要花费长达 72 小时。

如果通过 T-SQL 应用优化建议，则自动性能验证和反转机制不可用。 以这种方式应用的建议将保持活动状态，并在 24-48 小时显示在优化建议列表中。 将在 24-48 小时内保持活动状态并显示在建议列表中。 如果你想要更快地删除建议，可以通过 Azure 门户放弃它。

每个数据库都可以独立启用或禁用自动优化选项，也可以在服务器级别配置这些选项，并将其应用于从服务器继承设置的每个数据库。 服务器可继承 Azure 默认值，用于自动优化设置。 目前 Azure 默认值设为启用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，禁用 DROP_INDEX。

> [!IMPORTANT]
> 从 2020 年 3 月开始，对用于自动优化的 Azure 默认设置的更改将生效，如下所示：
>
> - 新的 Azure 默认设置为 FORCE_LAST_GOOD_PLAN = enabled、CREATE_INDEX = disabled 和 DROP_INDEX = disabled。
> - 未配置自动优化首选项的现有服务器将自动配置为继承新的 Azure 默认设置。 这适用于当前有用于自动优化的服务器设置处于未定义状态的所有客户。
> - 创建的新服务器将自动配置为继承新的 Azure 默认设置（与之前创建新服务器时自动优化配置处于未定义状态时不同）。

要配置自动优化，建议在服务器上配置自动优化选项并继承属于父级服务器的数据库设置，因为这会简化对大量数据库的自动优化选项的管理。

若要了解有关生成自动优化建议的电子邮件通知，请参阅 [自动优化电子邮件通知](automatic-tuning-email-notifications-configure.md)。

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Azure SQL 托管实例的自动优化

SQL 托管实例的自动优化仅支持“强制执行上一个卓越计划”。 有关通过 T-SQL 配置自动优化选项的详细信息，请参阅[自动优化引入了自动计划更正](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)和[自动计划更正](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)。

## <a name="next-steps"></a>后续步骤

- 若要了解自动优化中使用的内置智能，请参阅[人工智能优化 Azure SQL 数据库](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)。
- 若要了解自动优化的工作原理，请参阅[在 Microsoft Azure SQL 数据库中自动索引数百万个数据库](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)。
