---
title: "排查性能问题和优化数据库 | Microsoft Docs"
description: "将性能建议应用到 SQL 数据库，并了解如何获取有关针对数据库运行的查询的性能见解"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,monitor & tune
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: f9ae96cdc80c347593f229cb2fce3f2d4d8e7caf
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017

---
# <a name="troubleshoot-performance-issues-and-optimize-your-database"></a>排查性能问题和优化数据库

缺少索引与查询优化不足是数据库性能不佳的常见原因。 在本教程中，你将学习：
> [!div class="checklist"]
> * 查看、应用和撤销性能改善建议
> * 查找资源使用率较高的查询
> * 查找长时间运行的查询

> 需要针对存在性能问题（例如，缺少索引）的数据库持续运行一个工作负荷，以接收建议。
>

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="review-and-apply-a-recommendation"></a>查看并应用建议

遵循以下步骤，对数据库应用系统提供的建议：

1. 在数据库边栏选项卡中单击“性能建议”菜单。

    ![性能建议](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. 在建议列表中，选择一条有效的建议。 在本示例中，该建议为“创建索引”。

    ![选择建议](./media/sql-database-performance-tutorial/create_index.png)

3. 单击“应用”按钮应用该建议。 （可选）查看建议详细信息，然后单击“查看脚本”按钮查看要执行的 T-SQL 脚本。

    ![应用建议](./media/sql-database-performance-tutorial/apply.png)

4. [可选] 启用自动优化，以自动应用建议。

    ![自动优化](./media/sql-database-performance-tutorial/auto_tuning.png)

## <a name="revert-a-recommendation"></a>撤销建议

数据库顾问会监视实施的每条建议。 如果某条建议未改善工作负荷，该建议将自动被撤销。 可以手动撤销建议，但大多数情况下不需要这样做。 撤销建议：

1. 转到性能建议菜单，然后选择一条已应用的建议。

    ![选择建议](./media/sql-database-performance-tutorial/select.png)

2. 在详细信息视图中，单击“撤销”。

    ![撤销建议](./media/sql-database-performance-tutorial/revert.png)

## <a name="find-the-query-that-consumes-the-most-resources"></a>查找资源消耗量最大的查询

遵循以下步骤查找资源消耗量最大的查询：

1. 在数据库边栏选项卡中单击“查询性能见解”菜单。

    ![查询见解](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. 选择资源类型。

    ![查询见解](./media/sql-database-performance-tutorial/select_resource_type.png)

3. 选择表中的第一个查询。

    ![查询见解](./media/sql-database-performance-tutorial/select_query.png)

4. 查看查询详细信息。

    ![查询见解](./media/sql-database-performance-tutorial/query_details.png)

## <a name="find-the-longest-running-query"></a>查找运行时间最长的查询

1. 转到“查询性能见解”，然后选择“长时间运行的查询”选项卡。

    ![查询见解](./media/sql-database-performance-tutorial/long_running.png)

3. 选择表中的第一个查询。

    ![查询见解](./media/sql-database-performance-tutorial/select_first_query.png)

4. 查看查询详细信息。

    ![查询见解](./media/sql-database-performance-tutorial/review_query_details.png)



## <a name="next-steps"></a>后续步骤 
缺少索引与查询优化不足是数据库性能不佳的常见原因。 本教程介绍：
> [!div class="checklist"]
> * 查看、应用和撤销性能改善建议
> * 查找资源使用率较高的查询
> * 查找长时间运行的查询

[SQL 数据库性能调整提示](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

