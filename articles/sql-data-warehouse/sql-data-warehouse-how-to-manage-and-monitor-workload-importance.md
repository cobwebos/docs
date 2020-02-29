---
title: 管理和监视工作负荷重要性
description: 了解如何在 Azure Synapse 分析中管理和监视请求级别的重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6274bff9f9c57bfb06e58e1c4bfce6b6e265ac62
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195611"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>在 Azure Synapse 分析中管理和监视工作负荷重要性

使用 Dmv 和目录视图管理和监视 Azure Synapse 中的 SQL Analytics 请求级别重要性。

## <a name="monitor-importance"></a>监视重要性

使用[sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)动态管理视图中的 "新重要性" 列监视重要性。
以下监视查询显示了查询的提交时间和开始时间。 查看提交时间和开始时间以及重要性，以了解重要性影响计划的方式。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

若要进一步了解如何计划查询，请使用目录视图。

## <a name="manage-importance-with-catalog-views"></a>通过目录视图管理重要性

Sys. workload_management_workload_classifiers 目录视图包含有关分类器的信息。 若要排除映射到资源类的系统定义的分类器，请执行以下代码：

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

目录视图[workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)包含有关在分类器创建时使用的参数的信息。  下面的查询显示在包含 ExecutiveReports 的值 ```membername``` 参数上创建了 ExecReportsClassifier：

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![查询结果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

为了简化错误分类的故障排除，建议你在创建工作负荷分类器时删除资源类角色映射。 下面的代码返回现有资源类角色成员身份。 为从相应资源类返回的每个 ```membername``` 运行 sp_droprolemember。
下面是在删除工作负荷分类器之前检查是否存在的示例：

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>后续步骤
- 有关分类的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)。
- 有关重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [开始配置工作负荷重要性](sql-data-warehouse-how-to-configure-workload-importance.md)
