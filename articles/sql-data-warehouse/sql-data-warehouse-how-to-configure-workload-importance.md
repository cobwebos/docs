---
title: 配置工作负荷重要性
description: 了解如何设置请求级别重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692689"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>在 Azure SQL 数据仓库中配置工作负荷重要性

在 SQL 数据仓库中设置重要性使你可以影响查询计划。 重要性较高的查询将计划在重要性较低的查询之前运行。 若要为查询分配重要性，需要创建工作负荷分类器。

## <a name="create-a-workload-classifier-with-importance"></a>创建具有重要性的工作负荷分类器

通常，在数据仓库方案中，用户需要其查询快速运行。  用户可以是需要运行报告的公司高管，也可以是运行即席查询的分析师。 你可以创建一个工作负荷分类器来为查询分配重要性。  下面的示例使用新的 [create workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 语法来创建两个分类器。  Membername 可以是单个用户或组。 单个用户分类优先于角色分类。 若要查找现有数据仓库用户，请运行：

```sql
Select name from sys.sysusers
```

若要为重要性较高的用户创建工作负荷分类器，请运行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

若要为重要性较低运行即席查询的用户创建工作负荷分类器，请运行：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>后续步骤
- 有关工作负荷管理的详细信息，[请参阅工作负荷分类](sql-data-warehouse-workload-classification.md)
- 有关重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [开始管理和监视工作负荷重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
