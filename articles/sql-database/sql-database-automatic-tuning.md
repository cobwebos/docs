---
title: "SQL 数据库 - 自动优化 | Microsoft Docs"
description: "SQL 数据库可分析 SQL 查询并自动适应用户工作负荷。"
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>自动优化

Azure SQL 数据库是完全托管的数据服务，用于监视在数据库上执行的查询并且可自动提高工作负荷的性能。 Azure SQL 数据库具有内置智能机制，此机制可通过动态调整数据库使其适应工作负荷来自动优化和提高查询性能。 Azure SQL 数据库中的自动优化可能是可在 Azure SQL 数据库中启用的优化查询性能的最重要功能之一。

请参阅本文获取使用 Azure 门户[启用自动优化](sql-database-automatic-tuning-enable.md)的步骤。

## <a name="why-automatic-tuning"></a>为什么启用自动优化？

经典数据库管理的一项主要任务是监视工作负荷，识别重要的 SQL 查询、应添加用于提高性能的索引以及很少使用的索引。 Azure SQL 数据库提供针对需要监视的查询和索引的详细见解。 然而，持续监视数据库是一项艰巨且乏味的任务，尤其是在处理多个数据库时。 即使使用 Azure SQL 数据库和 Azure 门户提供的所有可用工具和报表，也可能无法高效管理大量数据库。 可考虑使用自动优化功能将某些监视和优化操作委派给 Azure SQL 数据库，而不是手动监视和优化数据库。 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>自动优化的工作原理是什么？

Azure SQL 数据库提供连续的性能监视和分析处理，可持续了解工作负荷的特征并识别潜在问题和改进方面。

![自动优化过程](./media/sql-database-automatic-tuning/tuning-process.png)

此过程能发现哪些索引和计划可能提高工作负荷性能以及哪些索引会影响工作负荷，Azure SQL 数据库可据此进行动态调整以适应工作负荷。 基于这些发现，自动优化将应用可提高工作负荷性能的优化操作。 此外，Azure SQL 数据库可在自动优化做出更改后持续监视性能，确保工作负荷性能得到提高。 未提高性能的任何操作都将自动还原。 此验证过程非常重要，可确保自动优化所做的任何更改都不会降低工作负荷性能。

Azure SQL 数据库可从两个方面进行自动优化：

 -  自动索引管理，用于标识应在数据库中添加的索引以及应删除的索引。
 -  自动计划更正（即将推出，已在 SQL Server 2017 中提供），用于标识有问题的计划并修复 SQL 计划性能问题。

## <a name="automatic-index-management"></a>自动索引管理

在 Azure SQL 数据库中进行索引管理很容易，因为 Azure SQL 数据库了解工作负荷并确保始终以最佳方式为数据编制索引。 正确的索引设计对于优化工作负荷性能至关重要，而自动索引管理也有助于优化索引。 自动索引管理可修复未正确编制索引的数据库中的性能问题，或者维护并改进现有数据库架构上的索引。 

### <a name="why-do-you-need-index-management"></a>为什么需要索引管理？

索引能加速某些从表中读取数据的查询；但会减慢更新数据的查询的速度。 需要仔细分析何时创建索引以及需在索引中包含哪些列。 某些时间后可能不需要某些索引。 因此，需要定期识别并删除没有任何益处的索引。 如果忽略未使用的索引，将导致更新数据的查询性能降低，对读取数据的查询也毫无益处。 未使用的索引也会影响系统整体性能，因为其他更新会需要不必要的日志记录。

既要改进从表中读取数据的查询的性能，又要最大限度地减少对更新数据的查询的影响，要找出此类最佳索引可能需要持续进行复杂的分析。

Azure SQL 数据库使用内置智能和高级规则分析查询，可识别最适用于当前工作负荷的索引以及可能需要删除的索引。 对于可优化读取数据的查询的索引，Azure SQL 数据库可确保将此类索引数量降至最低，同时将对其他查询的影响降至最低。

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>如何识别数据库中需要更改的索引？

Azure SQL 数据库可简化索引管理过程。 无需手动执行乏味的工作负荷分析和索引监视，Azure SQL 数据库可分析工作负荷、识别可使用新索引加速执行的查询，以及识别未使用或重复的索引。 有关如何识别应更改索引的详细信息，请参阅[在 Azure 门户中查找索引建议](sql-database-advisor-portal.md)。

### <a name="automatic-index-management-considerations"></a>自动索引管理注意事项

如果发现该内置规则提高了数据库的性能，你可能会用 Azure SQL 数据库自动管理索引。

在 Azure SQL 数据库中创建必要索引所需的操作可能会消耗资源并暂时影响工作负荷性能。 为将创建索引对工作负荷性能的影响降至最低，Azure SQL 数据库可为任何索引管理操作找到适当的时段。 如果数据库需要资源才能执行工作负荷，优化操作将被推迟，并在数据库有足够可用于维护任务的未使用资源时被启动。 验证操作是自动索引管理中的一项重要功能。 Azure SQL 数据库创建或删除索引后，监视进程将分析工作负荷的性能，验证该操作是否提高了性能。 如果未显著提高，操作将立即还原。 这样，Azure SQL 数据库便可确保自动操作不会对工作负荷性能产生负面影响。 自动优化创建的索引对在基础架构上进行的维护操作没有任何影响。 自动创建的索引不会阻止对架构进行更改（如删除或重命名列）。 相关表或列被删除时，Azure SQL 数据库自动创建的索引也将被立即删除。

## <a name="automatic-plan-choice-correction"></a>自动计划选择更正

自动计划更正是 SQL Server 2017 CTP2.0 中的新增自动优化功能，可识别行为错误的 SQL 计划。 此项针对 Azure SQL 数据库的自动优化选项即将推出。 有关详细信息，请参阅 [SQL Server 2017 中的自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并使自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请参阅 [Azure 门户中的优化建议](sql-database-advisor-portal.md)，然后手动应用一些可提高查询性能的建议。

