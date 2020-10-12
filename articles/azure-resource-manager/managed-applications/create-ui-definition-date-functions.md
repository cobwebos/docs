---
title: 创建 UI 定义日期函数
description: 介绍在使用日期值时要使用的函数。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095980"
---
# <a name="createuidefinition-date-functions"></a>创建 UI 定义日期函数

在使用日期值时要使用的函数。

## <a name="addhours"></a>addHours

向指定时间戳添加整小时数。

以下示例返回 `"1991-01-01T00:59:59.000Z"`：

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

向指定时间戳添加整分钟数。

以下示例返回 `"1991-01-01T00:00:59.000Z"`：

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
向指定时间戳添加整秒数。

以下示例返回 `"1991-01-01T00:00:00.000Z"`：

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

返回本地计算机上以 ISO 8601 格式显示的当前日期和时间的字符串。

以下示例可能返回 `"1990-12-31T23:59:59.000Z"`：

```json
"[utcNow()]"
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。
