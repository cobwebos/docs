---
title: sys. sp_cleanup_data_retention (Transact-sql) -Azure SQL Edge
description: '了解如何在 Azure SQL Edge 中使用 sp_cleanup_data_retention (Transact-sql) '
keywords: sys. sp_cleanup_data_retention (Transact-sql) ，SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933620"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**适用于：** Azure SQL Edge

从启用了数据保留策略的表中清除过时的记录。 有关详细信息，请参阅 [数据保留](data-retention-overview.md)。 

## <a name="syntax"></a>语法 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>自变量  
`[ @schema_name = ] schema_name`    
 需要执行清除的表所属架构的名称。 *schema_name* 是 **sysname**类型的必需参数。
  
`[ @table_name = ] 'table_name'`    
 需要执行清除操作的表的名称。 *table_name* 是 **sysname**类型的必需参数。

## <a name="output-parameter"></a>输出参数  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount 是一个可选的 OUTPUT 参数，表示从表中清除的记录数。 *rowcount* 为 int。
  
## <a name="permissions"></a>权限    
 需要 db_owner 权限。

## <a name="next-steps"></a>后续步骤
- [数据保留和自动数据清除](data-retention-overview.md)
- [用保留策略管理历史数据](data-retention-cleanup.md) 
- [启用和禁用数据保留](data-retention-enable-disable.md)
