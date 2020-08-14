---
title: Azure Cosmos DB 查询语言中的日期和时间函数
description: 了解 Azure Cosmos DB 中的日期和时间 SQL 系统函数，以便执行日期/时间和时间戳操作。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224945"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日期和时间函数 (Azure Cosmos DB)

通过日期和时间函数，可以在 Azure Cosmos DB 中执行 DateTime 和时间戳操作。

## <a name="functions-to-obtain-the-date-and-time"></a>用于获取日期和时间的函数

下面的标量函数允许您以两种格式获取当前的 UTC 日期和时间：符合 ISO 8601 格式的字符串，或值为 Unix epoch （以毫秒为单位）的数值时间戳：

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>用于处理 DateTime 值的函数

以下函数可让你轻松地处理日期时间值：

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>后续步骤

- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
- [用户定义函数](sql-query-udfs.md)
- [聚合](sql-query-aggregates.md)
