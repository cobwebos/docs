---
title: 应用性能建议
description: 使用 Azure 门户查找可优化数据库性能的性能建议。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: 72f0d361f69232894df3a9131d173411614a2055
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921208"
---
# <a name="find-and-apply-performance-recommendations"></a>查找并应用性能建议
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

可以使用 Azure 门户查找可优化 Azure SQL 数据库中数据库的性能或纠正在工作负载中识别的某种问题的性能建议。 使用 Azure 门户中的“性能建议”页可以根据建议的潜在影响查找最重要的建议。

## <a name="viewing-recommendations"></a>查看建议

若要查看和应用性能建议，需要 azure 中的[AZURE RBAC) 权限的正确 azure 基于角色的访问控制 (](../../role-based-access-control/overview.md) 。 查看建议需要“读取者”、“SQL DB 参与者”权限，执行任何操作（如创建或删除索引、取消创建索引）需要“所有者”、“SQL DB 参与者”权限。   

在 Azure 门户中使用以下步骤查找性能建议：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 转到“所有服务” > “SQL 数据库”，然后选择数据库。
3. 导航到“性能建议”，查看适用于所选数据库的可用建议。

性能建议会显示在类似于下图所示的表中：

![建议](./media/database-advisor-find-recommendations-portal/recommendations.png)

按其对性能的潜在影响将建议分为以下类别：

| 影响 | 说明 |
|:--- |:--- |
| 高 |高影响建议应提供最重要的性能影响。 |
| 中型 |中等影响建议应提高性能，但提升程度不大。 |
| 低 |低影响建议提供的性能比没有时更好，但改进可能不明显。 |

> [!NOTE]
> Azure SQL 数据库至少需要监视一天的活动才能识别某些建议。 Azure SQL 数据库优化一致的查询模式比优化随机的突发活动更加轻松。 如果建议当前不可用，“性能建议”页会提供一条说明原因的消息。

还可以查看历史操作的状态。 选择一条建议或状态即可查看详细信息。

下面是 Azure 门户中“创建索引”建议的示例。

![创建索引](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>应用建议

Azure SQL 数据库允许通过以下三个选项之一完全控制建议的启用方式：

* 一次应用一个建议。
* 启用自动优化以自动应用建议。
* 若要手动实施建议，请针对数据库运行建议的 T-SQL 脚本。

选择要查看其详细信息的建议，然后单击“查看脚本”查看具体详细信息，了解如何创建建议。

应用建议时，数据库将保持联机状态 -- 使用性能建议或自动优化不会使数据库脱机。

### <a name="apply-an-individual-recommendation"></a>应用单个建议

可以逐个查看和接受建议。

1. 在“建议”页上选择某个建议。
2. 在“详细信息”页上单击“应用”按钮 。

   ![应用建议](./media/database-advisor-find-recommendations-portal/apply.png)

所选的建议将在数据库上应用。

### <a name="removing-recommendations-from-the-list"></a>从列表中删除建议

如果建议的列表中包含你想要删除的项则可放弃建议：

1. 选择“建议”列表中的建议打开详细信息。
2. 单击“详细信息”页上的“放弃”。

可以根据需要将已放弃的项重新添加到“建议”列表中： 

1. 在“建议”页上单击“查看已放弃项”。
2. 从列表中选择一个放弃的项，查看其详细信息。
3. （可选）单击“撤消放弃”，将索引重新添加到“建议”的主列表。

> [!NOTE]
> 请注意，如果已启用 SQL 数据库[自动优化](automatic-tuning-overview.md)，并且如果已手动放弃列表中的建议，则此类建议将永远不会自动应用。 放弃建议是让用户能够在需要某条特定建议不应当应用的情况下启用自动优化的方便方法。
> 可以通过选择“撤消放弃”选项将放弃的建议添加回建议列表来还原此行为。

### <a name="enable-automatic-tuning"></a>启用自动优化

可以将数据库设置为自动实施建议。 建议变为可用时，会自动应用。 与该服务所管理的所有建议一样，如果存在负面的性能影响，则会还原建议。

1. 在“建议”页上单击“自动化”：

   ![索引顾问设置](./media/database-advisor-find-recommendations-portal/settings.png)
2. 选择要自动执行的操作：

   ![建议的索引](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> 请注意，**DROP_INDEX** 选项当前与使用分区切换和索引提示的应用程序不兼容。

选择所需配置后，请单击“应用”。

### <a name="manually-apply-recommendations-through-t-sql"></a>通过 T-SQL 手动应用建议

选择任意建议，然后单击“查看脚本”。 针对数据库运行此脚本以手动应用建议。

不通过该服务监视和验证手动执行的索引的性能影响，因此建议在创建后监视这些索引以验证它们是否提供性能提升，并在必要时调整或删除它们。 有关创建索引的详细信息，请参阅 [CREATE INDEX (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。 此外，手动应用的建议在系统自动撤消它们之前， 将在 24-48 小时内保持活动状态并显示在建议列表中。 如果你想要更快地删除建议，可以手动放弃它。

### <a name="canceling-recommendations"></a>取消建议

可以取消处于“待定”、“正在验证”或“成功”状态的建议。 不能取消状态为“正在执行”的建议。

1. 在“优化历史记录”区域中选择建议，打开“建议详细信息”页。
2. 单击“取消”可中止应用建议的过程。

## <a name="monitoring-operations"></a>监视操作

可能不会立刻应用建议。 该门户提供了有关建议状态的详细信息。 以下是索引可能处于的状态：

| 状态 | 说明 |
|:--- |:--- |
| 挂起 |已经接收应用建议命令并计划执行。 |
| 执行 |正在应用建议。 |
| 正在验证 |已成功应用建议，服务正在权衡优势。 |
| Success |已成功应用建议，并且已权衡优势。 |
| 错误 |在应用建议的过程期间发生了错误。 这可能是暂时性问题，或可能是表的架构更改所致，并且脚本不再有效。 |
| 还原 |已应用建议，但该建议被认为是非性能的且正在被自动还原。 |
| 已还原 |已还原建议。 |

单击列表中的进程内建议即可查看详细信息：

![建议的索引](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>正在还原建议

如果使用了性能建议来应用建议（即没有手动运行 T-SQL 脚本），那么性能建议会在发现性能影响为负面时自动还原该更改。 如果因故只想要还原建议，可执行以下操作：

1. 在“优化历史记录”区域中选择已成功应用的建议。
2. 单击“建议详细信息”页上的“还原”。

![建议的索引](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>监视索引建议的性能影响

成功实施建议后（当前仅限索引操作和参数化查询建议），可单击“建议详细信息”页上的“查询见解”打开[查询性能见解](query-performance-insight-use.md)，并查看排位靠前的查询的性能影响。

![监视性能影响](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>摘要

Azure SQL 数据库提供可提高数据库性能的建议。 通过提供 T-SQL 脚本，可以获得帮助来优化数据库并最终提升查询性能。

## <a name="next-steps"></a>后续步骤

监视建议并继续应用它们以优化性能。 数据库工作负荷是动态的，并且不断地更改。 Azure SQL 数据库将继续监视和提供可能提高数据库性能的建议。

* 请参阅[自动优化](automatic-tuning-overview.md)详细了解 Azure SQL 数据库中的自动优化。
* 请参阅[性能建议](database-advisor-implement-performance-recommendations.md)，获取 Azure SQL 数据库性能建议的概述。
* 请参阅[查询性能见解](query-performance-insight-use.md)，了解排名靠前的查询的性能影响。

## <a name="additional-resources"></a>其他资源

* [查询存储](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Azure RBAC) 的 azure 基于角色的访问控制 (](../../role-based-access-control/overview.md)
