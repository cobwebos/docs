---
title: Azure Cosmos DB 查询语言中的 PI
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 PI。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349647"
---
# <a name="pi-azure-cosmos-db"></a>PI （Azure Cosmos DB）
 返回 PI 的常数值。  
  
## <a name="syntax"></a>语法
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例返回 @no__t 的值。  
  
```sql
SELECT PI() AS pi 
```  
  
 结果集如下。  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
