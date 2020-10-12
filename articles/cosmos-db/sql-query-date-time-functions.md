---
title: Azure Cosmos DB 查询语言中的日期和时间函数
description: 了解 Azure Cosmos DB 中的日期和时间 SQL 系统函数，以便执行日期/时间和时间戳操作。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605188"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日期和时间函数 (Azure Cosmos DB)

通过日期和时间函数，可以在 Azure Cosmos DB 中执行 DateTime 和时间戳操作。

## <a name="functions-to-obtain-the-date-and-time"></a>用于获取日期和时间的函数

使用以下标量函数可以获取采用以下三种格式的当前 UTC 日期和时间：一个字符串（符合 ISO 8601 格式）、一个数值时间戳（其值为自 Unix 纪元以来已经过的毫秒数），或另一个数值时间戳（其值为自 Unix 纪元以来已经过的时长 100 纳秒的时钟周期数）：

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>用于处理 DateTime 值的函数

以下函数使你可以轻松地处理日期/时间、时间戳和时钟周期值：

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>后续步骤

- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
- [用户定义的函数](sql-query-udfs.md)
- [聚合](sql-query-aggregates.md)
