---
title: Azure Cosmos DB 查询语言中的 GetCurrentTicks
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 GetCurrentTicks。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606933"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB) 

返回自00:00:00 星期四（100年 1 1970 月1日）起已经过的毫微秒计时周期数。
  
## <a name="syntax"></a>语法
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>返回类型

返回一个有符号的数字值，从 Unix epoch 开始，当前的100毫微秒计时周期数。 换句话说，GetCurrentTicks 返回自00:00:00 星期四（100年 1 1970 月1日）起已经过的毫微秒计时周期数。

## <a name="remarks"></a>注解

GetCurrentTicks ( # A1 是不确定性函数。 返回的结果采用 UTC（协调世界时）格式。

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
