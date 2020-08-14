---
title: Azure Cosmos DB 查询语言中的 GetCurrentTicks
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 GetCurrentTicks。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227073"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB) 

返回以计时周期度量的当前日期和时间。
  
## <a name="syntax"></a>语法
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>返回类型

返回一个正整数值。

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="examples"></a>示例

下面的示例返回当前时间，以刻度为单位：

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
