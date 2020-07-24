---
title: 创建 UI 定义集合函数
description: 描述在使用集合（如数组和对象）时要使用的函数。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095982"
---
# <a name="createuidefinition-collection-functions"></a>CreateUiDefinition 集合函数

这些函数可用于集合，如 JSON 字符串、数组和对象。

## <a name="contains"></a>contains

如果字符串包含指定子字符串、数组包含指定值，或对象包含指定键，则返回 `true`。

### <a name="example-string-contains"></a>示例： string contains

以下示例返回 `true`：

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>示例：数组包含

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `false`：

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>示例：对象包含

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

以下示例返回 `true`：

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>empty

如果字符串、数组或对象为 null 或为空，则返回 `true`。

### <a name="example-string-empty"></a>示例：字符串为空

以下示例返回 `true`：

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>示例：数组为空

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `false`：

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>示例：对象为空

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

以下示例返回 `false`：

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>示例： null 和 undefined

假设 `element1` 为 `null` 或未定义。 以下示例返回 `true`：

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

在应用作为 lambda 函数提供的筛选逻辑后，返回新数组。 第一个参数是用于筛选的数组。 第二个参数是指定筛选逻辑的 lambda 函数。

下面的示例返回数组 `[ { "name": "abc" } ]` 。

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>first

返回指定字符串的第一个字符、指定数组的第一个值，或指定对象的第一个键和值。

### <a name="example-string-first"></a>示例：先字符串

以下示例返回 `"c"`：

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>示例：数组优先

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `1`：

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>示例：对象优先

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

以下示例返回 `{"key1": "Linux"}`：

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>last

返回指定字符串的最后一个字符、指定数组的最后一个值，或指定对象的最后一个键和值。

### <a name="example-string-last"></a>示例：最后一个字符串

以下示例返回 `"o"`：

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>示例：数组 last

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `3`：

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>示例：对象上次

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

以下示例返回 `{"key2": "Windows"}`：

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

返回字符串中的字符数、数组中的值数，或对象中的键数。

### <a name="example-string-length"></a>示例：字符串长度

以下示例返回 `7`：

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>示例：数组 length

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `3`：

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>示例：对象长度

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

以下示例返回 `2`：

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

在对提供的数组调用 lambda 函数后返回新数组。 第一个参数是要用于 lambda 函数的数组。 第二个参数是 lambda 函数。

下面的示例返回一个新数组，其中每个值都成对出现。 结果为 `[2, 4, 6]`。

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

下面的示例返回一个新的数组 `["abc", "xyz"]` 。

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

跳过集合中指定数量的元素，返回其余元素。

### <a name="example-string-skip"></a>示例：字符串 skip

以下示例返回 `"app"`：

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>示例：数组 skip

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `[3]`：

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>示例：对象 skip

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
以下示例返回 `{"key2": "Windows"}`：

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>拆分式

返回一个字符串数组，其中包含由分隔符分隔的输入的子字符串。

下面的示例返回数组 `[ "555", "867", "5309" ]` 。

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

返回字符串起始处指定数量的连续字符、数组起始处指定数量的连续值，以及对象起始处的指定数量的连续键和值。

### <a name="example-string-take"></a>示例：字符串 take

以下示例返回 `"web"`：

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>示例：数组 take

假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `[1, 2]`：

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>示例：对象 take

假设 `element1` 返回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

以下示例返回 `{"key1": "Linux"}`：

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。
