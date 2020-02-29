---
title: 配置工作负荷重要性
description: 了解如何在 Azure Synapse 分析中设置请求级别重要性。
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
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196791"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中配置工作负荷重要性

在 SQL Analytics for Azure Synapse 中设置重要性使你可以影响查询计划。 重要性较高的查询将计划在重要性较低的查询之前运行。 若要为查询分配重要性，需要创建工作负荷分类器。

## <a name="create-a-workload-classifier-with-importance"></a>创建具有重要性的工作负荷分类器

通常在数据仓库方案中，用户需要其查询才能快速运行。  用户可能是需要运行报表的公司的主管，或者用户可能是运行即席查询的分析师。 创建工作负荷分类器可为查询指定重要性。  下面的示例使用新的[create 工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)语法来创建两个分类器。  成员名称可以是单个用户或组。 单个用户分类优先于角色分类。 若要查找现有数据仓库用户，请运行：

```sql
Select name from sys.sysusers
```

若要为重要性较高的用户创建工作负荷分类器，请执行以下操作：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

若要为运行具有较低重要性的即席查询的用户创建工作负荷分类器，请执行以下操作：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>后续步骤
- 有关工作负荷管理的详细信息，请参阅[工作负荷分类](sql-data-warehouse-workload-classification.md)
- 有关重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [请参阅管理和监视工作负荷重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
