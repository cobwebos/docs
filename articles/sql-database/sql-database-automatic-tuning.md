---
title: "Azure SQL 数据库 - 自动优化 | Microsoft Docs"
description: "Azure SQL 数据库可分析 SQL 查询并自动适应用户工作负荷。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/09/2017
ms.author: jovanpop
ms.openlocfilehash: dcea2c34ab26349c6f937c582bfe35d7a42f24a6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL 数据库中的自动优化

Azure SQL 数据库自动优化通过利用人工智能的持续性能优化，进而提供最佳性能和稳定的工作负载。

自动优化是一项完全托管的服务，它使用内置的智能来持续监视在数据库上执行的查询，并自动提高其性能。 这是通过动态调整数据库以适应不断变化的工作负载并应用优化建议来实现的。 自动优化通过人工智能向 Azure 上的所有数据库进行横向学习，并动态地提高其优化操作。 在开启自动优化时，Azure SQL 数据库运行时间越长，执行能力就越好。

Azure SQL 数据库自动优化可能是可用于提供稳定及最佳执行工作负载的最重要的功能之一。

## <a name="what-can-automatic-tuning-do-for-you"></a>自动优化有何作用？

- Azure SQL 数据库的自动性能优化
- 性能优化的自动验证
- 自动回退和自我更正
- 优化历史记录日志
- 优化用于手动部署的 T-SQL 脚本
- 主动的工作负载性能监视
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="safe-reliable-and-proven"></a>安全、可靠且久经考验

Azure SQL 数据库所应用的优化操作非常安全，可满足最紧张工作负载的性能需求。 系统精心设计，旨在不干扰用户工作负载。 仅在利用率较低的情况下才应用自动优化建议。 系统还可暂时禁用自动优化操作以保护工作负载性能。 在此情况下，Azure 门户中将显示“由系统禁用”消息。 自动优化对工作负载持以最高的资源优先级。

自动优化机制非常成熟，已经在 Azure 上运行的成百上千个数据库中得到完善。 应用的自动优化操作将会自动验证，确保工作负载性能得到显著改善。 动态检测针对退化性能的建议并立即优化。 通过优化历史记录日志，可清楚地跟踪对每个 Azure SQL 数据库进行的优化改进。 

![自动优化的工作原理](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL 数据库自动优化与 SQL Server 自动优化引擎共享其核心逻辑。 有关内置智能机制的其他技术信息，请参阅 [SQL Server 自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。

## <a name="use-automatic-tuning"></a>使用自动优化

需在订阅上手动启用自动优化。 若要使用 Azure 门户启用自动优化，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。

自动优化可通过自动应用优化建议（包括性能提升的自动验证）自主操作。 

要获取更多掌控力，可关闭“自动应用优化建议”，还可通过 Azure 门户手动应用优化建议。 也可使用该解决方案仅查看自动优化建议，并通过所选脚本和工具手动应用。 

若要了解自动优化工作原理的概述以及典型使用方案，请观看嵌入视频：


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>自动优化选项

Azure SQL 数据库中可用的自动优化选项包括：
 1. 创建索引：用于标识可以提高工作负载性能的索引，创建索引，并验证它们是否可提高查询性能。
 2. 删除索引：标识冗余和重复索引，以及长期未使用的索引。
 3. 强制执行上一个卓越计划：确定当前使用执行计划的 SQL 查询速度比上一个卓越计划的慢，并使用上一个已知卓越计划（而非回归计划）。

Azure SQL 数据库确定可优化数据库的“创建索引”、“删除索引”和“强制执行上一个卓越计划”建议，并在 Azure 门户中显示它们。 有关如何识别应更改索引的详细信息，请参阅[在 Azure 门户中查找索引建议](sql-database-advisor-portal.md)。 可以使用门户手动应用建议，也可以让 Azure SQL 数据库自动应用建议，在更改后监视工作负载，并验证该建议是否改进了工作负载的性能。

每个数据库都可以独立打开或关闭自动优化选项，也可以在逻辑服务器上配置这些选项，并将其应用于从服务器继承设置的每个数据库。 要配置自动优化，建议在服务器上配置自动优化选项并继承服务器中数据库的设置，因为这会简化对大量数据库的自动优化选项的管理。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化以管理工作负载，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要手动查看并应用自动优化建议，请参阅[查找并应用性能建议](sql-database-advisor-portal.md)。
- 若要了解有关生成自动优化建议的电子邮件通知，请参阅[电子邮件通知以进行自动优化](sql-database-automatic-tuning-email-notifications.md)
- 若要详细了解自动优化中使用的内置智能，请参阅 [Artificial Intelligence tunes Azure SQL Databases](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)（人工智能优化 Azure SQL 数据库）。
- 若要了解有关 Azure SQL 数据库和 SQL Server 2017 中自动优化工作原理的详细信息，请参阅 [SQL Server 自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。
