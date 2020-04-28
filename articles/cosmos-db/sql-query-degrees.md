---
title: Azure Cosmos DB 查询语言中的 DEGREES
description: 了解 Azure Cosmos DB 中的 DEGREES SQL 系统函数，该函数返回指定角度（以弧度为单位）的相应角度（以度为单位）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299464"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
 返回指定角度（弧度）的相应角度（度）。  
  
## <a name="syntax"></a>语法
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回 PI/2 弧度角的度数。  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 下面是结果集：  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
