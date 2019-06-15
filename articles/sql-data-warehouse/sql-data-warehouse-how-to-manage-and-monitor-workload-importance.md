---
title: 管理和监视 Azure SQL 数据仓库中的工作负荷重要性 |Microsoft Docs
description: 了解如何管理和监视请求级别重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235928"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>管理和监视 Azure SQL 数据仓库中的工作负荷重要性

管理和监视 Azure SQL 数据仓库使用 Dmv 和目录视图中的请求级别重要性。

## <a name="monitor-importance"></a>监视器重要性

监视使用中的新重要性列的重要性[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)动态管理视图。
以下监视查询显示提交时间和查询的开始时间。 查看提交时间，开始时间和查看重要性如何影响计划的重要性。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

若要进一步查看到查询正在计划，请使用目录视图。

## <a name="manage-importance-with-catalog-views"></a>管理与目录视图的重要性

Sys.workload_management_workload_classifiers 目录视图包含在 Azure SQL 数据仓库实例中的分类器的信息。 若要排除的系统定义的分类器将映射到资源类，请执行以下代码：

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

目录视图中， [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)，包含有关在分类器的创建过程中使用的参数的信息。  以下查询显示了在创建 ExecReportsClassifier ```membername``` ExecutiveReports 具有的值的参数：

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![查询结果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

为了简化分类错误的排查，我们建议在创建工作负荷分类器时删除资源类角色映射。 以下代码返回现有的资源类角色成员身份。 每个运行 sp_droprolemember```membername```返回从相应的资源类。
下面是检查存在，然后再删除工作负荷分类器的示例：

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>后续步骤
- 分类的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)。
- 重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [转到配置工作负荷重要性 ](sql-data-warehouse-how-to-configure-workload-importance.md)
