---
title: Azure Cosmos DB 查询语言中的 ASIN
description: 了解 Azure Cosmos DB 中的反正弦（ASIN） SQL 系统函数如何返回以弧度表示的角，其正弦是指定的数值表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871732"
---
# <a name="asin-azure-cosmos-db"></a>ASIN （Azure Cosmos DB）
 返回角度（弧度），其正弦是指定的数值表达式。 也被称为反正弦。  
  
## <a name="syntax"></a>语法
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例返回-1 的 `ASIN`。  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 结果集如下。  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
