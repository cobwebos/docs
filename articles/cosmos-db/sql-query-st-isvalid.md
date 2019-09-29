---
title: Azure Cosmos DB 查询语言中的 ST_ISVALID
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ST_ISVALID。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349368"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID （Azure Cosmos DB）
 返回一个布尔值，指示指定的 GeoJSON 点、多边形或 LineString 表达式是否有效。  
  
## <a name="syntax"></a>语法
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*spatial_expr*  
   是 GeoJSON 点、多边形或 LineString 表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  以下示例介绍了如何使用 ST_VALID 检查点是否有效。  
  
  例如，由于此点具有一个不在有效值范围 [-90，90] 内的纬度值，因此查询返回 false。  
  
  对于多边形，GeoJSON 规范要求提供的最后一个坐标対应该与第一个坐标对相同，才能创建一个闭合形状。 多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 结果集如下。  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>后续步骤

- [空间函数 Azure Cosmos DB](sql-query-spatial-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
