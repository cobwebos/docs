---
title: Azure Cosmos DB 查询语言中的 ASIN
description: 了解 Azure Cosmos DB 中的反正弦（ASIN） SQL 系统函数如何返回以弧度表示的角，其正弦是指定的数值表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302689"
---
# <a name="asin-azure-cosmos-db"></a>ASIN （Azure Cosmos DB）
 返回角度（弧度），其正弦是指定的数值表达式。 也称为反正弦。  
  
## <a name="syntax"></a>语法
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例返回-1 的 `ASIN`。  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 下面是结果集：  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
