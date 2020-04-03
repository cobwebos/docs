---
title: 配置工作负荷重要性
description: 了解如何在 Azure 突触分析中设置请求级别重要性。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 4aa0e7da1e77334a8b9048dab4a461133f220bd1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582452"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>在 Azure 突触分析中配置工作负载重要性

在 Azure Synapse Synapse 的 Synapse SQL 中设置重要性允许您影响查询的调度。 重要性较高的查询将计划在重要性较低的查询之前运行。 若要为查询分配重要性，需要创建工作负荷分类器。

## <a name="create-a-workload-classifier-with-importance"></a>创建具有重要性的工作负荷分类器

通常，在数据仓库方案中，用户需要其查询快速运行。  用户可以是需要运行报告的公司高管，也可以是运行即席查询的分析师。 你可以创建一个工作负荷分类器来为查询分配重要性。  下面的示例使用新的 [create workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 语法来创建两个分类器。  Membername 可以是单个用户或组。 单个用户分类优先于角色分类。 若要查找现有数据仓库用户，请运行：

```sql
Select name from sys.sysusers
```

若要为重要性较高的用户创建工作负荷分类器，请运行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

若要为重要性较低运行即席查询的用户创建工作负荷分类器，请运行：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>后续步骤
- 有关工作负荷管理的详细信息，[请参阅工作负荷分类](sql-data-warehouse-workload-classification.md)
- 有关重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [开始管理和监视工作负荷重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
