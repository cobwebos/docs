---
title: Azure Cosmos DB 查询语言中的 DateTimeToTicks
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 DateTimeToTicks。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2e2c9e8f2bf0d4760bf030fb19a90737cdb54525
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605137"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB) 

将指定的 DateTime 转换为刻度。 一个计时周期表示一百纳秒，即一千万分之一秒。 

## <a name="syntax"></a>语法
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>参数
  
*DateTime*  
   格式的 UTC 日期和时间 ISO 8601 字符串值 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>返回类型

返回一个有符号的数字值，从 Unix epoch 开始，当前的100毫微秒计时周期数。 换句话说，DateTimeToTicks 返回自00:00:00 星期四（100年 1 1970 月1日）起已经过的毫微秒计时周期数。

## <a name="remarks"></a>注解

`undefined`如果 DateTime 不是有效的 ISO 8601 datetime，则 DateTimeDateTimeToTicks 将返回

此系统函数不会使用索引。

## <a name="examples"></a>示例

下面是返回刻度数的示例：

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

下面的示例返回刻度数，而不指定秒的小数部分：

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>后续步骤

- [日期和时间函数 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
