---
title: Azure SQL 数据仓库中配置工作负荷重要性 |Microsoft Docs
description: 了解如何设置请求级别重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 10a1fe2bff43b6799f47fc0245802ce578ef8f8f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165533"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中配置工作负荷重要性

设置 SQL 数据仓库中的重要性，可影响的查询计划。 具有较高的优先级的查询将计划之前查询运行具有较低的重要性。 若要分配给查询的重要性，需要创建工作负荷分类器。

## <a name="create-a-workload-classifier-with-importance"></a>创建具有重要性的工作负荷分类器

通常在数据仓库方案中具有需要其快速运行的查询的用户。  用户可能是公司的行政人员需运行报表或用户可能是运行即席查询的分析师。 创建工作负荷分类器，要分配给查询的重要性。  下面的示例使用新[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)语法来创建两个分类器。  成员名称可以是单个用户或组。 单个用户分类的优先级高于角色分类。 若要查找现有数据仓库用户，请运行：

```sql
Select name from sys.sysusers
```

若要创建工作负荷分类器，对于具有较高的优先级的用户运行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

若要创建运行即席查询与运行的重要性较低的用户的工作负荷分类器：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>后续步骤
- 有关工作负荷管理的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)
- 重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [转到管理和监视工作负荷重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
