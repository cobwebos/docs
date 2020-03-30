---
title: Azure Cosmos DB 查询语言中的 ST_DISTANCE
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ST_DISTANCE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537289"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 返回两个 GeoJSON 点、多边形、多面或线弦表达式之间的距离。 要了解更多信息，请参阅[地理空间和地理JSON位置数据](sql-query-geospatial-intro.md)一文。
  
## <a name="syntax"></a>语法
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>自变量
  
*spatial_expr*  
   为任何有效的 GeoJSON 点、多边形或 LineString 对象表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回包含距离的数值表达式。 默认参考系统以米为单位表示。  
  
## <a name="examples"></a>示例
  
  以下示例演示了如何使用 `ST_DISTANCE` 内置函数返回与指定位置的距离在 30 公里内的所有家族文档。 .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 下面是结果集：  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>备注

该系统功能将受益于[地理空间索引](index-policy.md#spatial-indexes)。

## <a name="next-steps"></a>后续步骤

- [空间函数 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系统功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 简介](introduction.md)
