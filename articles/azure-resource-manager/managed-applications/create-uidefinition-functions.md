---
title: 创建 UI 定义函数
description: 介绍为 Azure 托管应用程序构造 UI 定义时要使用的函数
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980807"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition 函数
本部分包含 CreateUiDefinition 支持的所有函数的签名。

若要使用函数，请使用方括号将调用括起来。 例如：

```json
"[function()]"
```

可将字符串和其他函数作为函数的参数进行引用，但字符串必须放在单引号内。 例如：

```json
"[fn1(fn2(), 'foobar')]"
```

如果适用，可以使用点运算符来引用函数的输出属性。 例如：

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>引用函数
可以使用这些函数引用属性的输出或 CreateUiDefinition 的上下文。

### <a name="basics"></a>basics
返回在 Basics 步骤中定义的元素的输出值。

下面的示例返回 Basics 步骤中名为 `foo` 的元素的输出：

```json
"[basics('foo')]"
```

### <a name="steps"></a>steps
返回在指定步骤中定义的元素的输出值。 若要获取 Basics 步骤中的元素的输出值，请改用 `basics()`。

下面的示例返回 `bar` 步骤中名为 `foo` 的元素的输出：

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
返回 Basics 步骤或当前上下文中选择的位置。

以下示例可能返回 `"westus"`：

```json
"[location()]"
```

## <a name="string-functions"></a>字符串函数
这些函数仅可用于 JSON 字符串。

### <a name="concat"></a>concat
连接一个或多个字符串。

例如，如果 `element1` 的输出值是 `"bar"`，则此示例返回字符串 `"foobar!"`：

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
返回指定字符串的子字符串。 该子字符串在指定的索引处开始，并且具有指定的长度。

以下示例返回 `"ftw"`：

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
返回一个字符串，其中，当前字符串中出现的所有指定字符串均替换为另一个字符串。

以下示例返回 `"Everything is awesome!"`：

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
生成全局唯一字符串 (GUID)。

以下示例可能返回 `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`：

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
返回转换成小写形式的字符串。

以下示例返回 `"foobar"`：

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
返回转换成大写形式的字符串。

以下示例返回 `"FOOBAR"`：

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>集合函数
这些函数可用于集合，如 JSON 字符串、数组和对象。

### <a name="contains"></a>contains
如果字符串包含指定子字符串、数组包含指定值，或对象包含指定键，则返回 `true`。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `true`：

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `false`：

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

以下示例返回 `true`：

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>长度
返回字符串中的字符数、数组中的值数，或对象中的键数。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `6`：

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `3`：

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

以下示例返回 `2`：

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
如果字符串、数组或对象为 null 或为空，则返回 `true`。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `true`：

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `false`：

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

以下示例返回 `false`：

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>示例 4：null 和未定义
假设 `element1` 为 `null` 或未定义。 以下示例返回 `true`：

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
返回指定字符串的第一个字符、指定数组的第一个值，或指定对象的第一个键和值。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `"f"`：

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `1`：

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
以下示例返回 `{"key1": "foobar"}`：

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
返回指定字符串的最后一个字符、指定数组的最后一个值，或指定对象的最后一个键和值。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `"r"`：

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `2`：

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

以下示例返回 `{"key2": "raboof"}`：

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
返回字符串起始处指定数量的连续字符、数组起始处指定数量的连续值，以及对象起始处的指定数量的连续键和值。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `"foo"`：

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `[1, 2]`：

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

以下示例返回 `{"key1": "foobar"}`：

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
跳过集合中指定数量的元素，返回其余元素。

#### <a name="example-1-string"></a>示例 1：字符串
以下示例返回 `"bar"`：

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>示例 2：数组
假设 `element1` 返回 `[1, 2, 3]`。 以下示例返回 `[3]`：

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>示例 3：对象
假设 `element1` 返回：

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
以下示例返回 `{"key2": "raboof"}`：

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>逻辑函数
可以在条件语句中使用这些函数。 某些函数可能不支持所有的 JSON 数据类型。

### <a name="equals"></a>equals
如果两个参数具有相同的类型和值，则返回 `true`。 此函数支持所有 JSON 数据类型。

以下示例返回 `true`：

```json
"[equals(0, 0)]"
```

以下示例返回 `true`：

```json
"[equals('foo', 'foo')]"
```

以下示例返回 `false`：

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
如果第一个参数严格小于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `true`：

```json
"[less(1, 2)]"
```

以下示例返回 `false`：

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
如果第一个参数小于或等于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `true`：

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
如果第一个参数严格大于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `false`：

```json
"[greater(1, 2)]"
```

以下示例返回 `true`：

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
如果第一个参数大于或等于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `true`：

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>and
如果所有参数的计算结果都为 `true`，则返回 `true`。 此函数仅支持布尔类型的两个或两个以上参数。

以下示例返回 `true`：

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

以下示例返回 `false`：

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>或
如果至少有一结个参数的计算果为 `true`，则返回 `true`。 此函数仅支持布尔类型的两个或两个以上参数。

以下示例返回 `true`：

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

以下示例返回 `true`：

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
如果参数的计算结果为 `true`，则返回 `false`。 此函数仅支持布尔类型的参数。

以下示例返回 `true`：

```json
"[not(false)]"
```

以下示例返回 `false`：

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
返回第一个非 null 参数的值。 此函数支持所有 JSON 数据类型。

假设未定义 `element1` 和 `element2`。 以下示例返回 `"foobar"`：

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

此函数在页面加载后由于用户操作而发生的可选调用上下文中尤其有用。 例如，如果在用户界面中的一个字段上放置的约束依赖于当前所选的另一个**不可见**字段的值，则为。 在这种情况`coalesce()`下，可以在页面加载时使用来允许函数在语法上有效，同时在用户与字段交互时具有所需的效果。

请考虑`DropDown`这样，这允许用户从多个不同的数据库类型中进行选择：

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

若要在此字段的当前选定值上对另一字段的操作进行`coalesce()`条件，请使用，如下所示：

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

这是必需的`databaseType` ，因为最初不可见，因此不包含值。 这将导致整个表达式计算不正确。

## <a name="conversion-functions"></a>转换函数
可以使用这些函数在 JSON 数据类型和编码之间转换值。

### <a name="int"></a>int
将参数转换为整数。 此函数支持数字和字符串类型的参数。

以下示例返回 `1`：

```json
"[int('1')]"
```

以下示例返回 `2`：

```json
"[int(2.9)]"
```

### <a name="float"></a>浮动
将参数转换为浮点。 此函数支持数字和字符串类型的参数。

以下示例返回 `1.0`：

```json
"[float('1.0')]"
```

以下示例返回 `2.9`：

```json
"[float(2.9)]"
```

### <a name="string"></a>string
将参数转换为字符串。 此函数支持所有 JSON 数据类型的参数。

以下示例返回 `"1"`：

```json
"[string(1)]"
```

以下示例返回 `"2.9"`：

```json
"[string(2.9)]"
```

以下示例返回 `"[1,2,3]"`：

```json
"[string([1,2,3])]"
```

以下示例返回 `"{"foo":"bar"}"`：

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
将参数转换为布尔值。 此函数支持数字、字符串和布尔类型的参数。 与 JavaScript 中的布尔值相似，`0` 或 `'false'` 以外的任何值都返回 `true`。

以下示例返回 `true`：

```json
"[bool(1)]"
```

以下示例返回 `false`：

```json
"[bool(0)]"
```

以下示例返回 `true`：

```json
"[bool(true)]"
```

以下示例返回 `true`：

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
将参数转换为本机类型。 换而言之，此函数是 `string()` 函数的反函数。 此函数仅支持字符串类型的参数。

以下示例返回 `1`：

```json
"[parse('1')]"
```

以下示例返回 `true`：

```json
"[parse('true')]"
```

以下示例返回 `[1,2,3]`：

```json
"[parse('[1,2,3]')]"
```

以下示例返回 `{"foo":"bar"}`：

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
将参数编码为 base-64 编码的字符串。 此函数仅支持字符串类型的参数。

以下示例返回 `"Zm9vYmFy"`：

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
将参数从 base-64 编码的字符串中解码。 此函数仅支持字符串类型的参数。

以下示例返回 `"foobar"`：

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
将参数编码为 URL 编码的字符串。 此函数仅支持字符串类型的参数。

以下示例返回 `"https%3A%2F%2Fportal.azure.com%2F"`：

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
将参数从 URL 编码的字符串中解码。 此函数仅支持字符串类型的参数。

以下示例返回 `"https://portal.azure.com/"`：

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>数学函数
### <a name="add"></a>add
将两个数字相加并返回结果。

以下示例返回 `3`：

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
从第一个数字中减去第二个数字并返回结果。

以下示例返回 `1`：

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
将两个数字相乘并返回结果。

以下示例返回 `6`：

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
用第一个数字除以第二个数字并返回结果。 结果始终是整数。

以下示例返回 `2`：

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
用第一个数字除以第二个数字并返回余数。

以下示例返回 `0`：

```json
"[mod(6, 3)]"
```

以下示例返回 `2`：

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
返回两个数字中的较小数。

以下示例返回 `1`：

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
返回两个数字中的较大数。

以下示例返回 `2`：

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
生成指定范围内的整数序列。

以下示例返回 `[1,2,3]`：

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
生成指定范围内的随机整数。 此函数不生成经过加密的安全随机数字。

以下示例可能返回 `42`：

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
返回小于或等于指定数字的最大整数。

以下示例返回 `3`：

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
返回大于或等于指定数字的最大整数。

以下示例返回 `4`：

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>日期函数
### <a name="utcnow"></a>utcNow
返回本地计算机上以 ISO 8601 格式显示的当前日期和时间的字符串。

以下示例可能返回 `"1990-12-31T23:59:59.000Z"`：

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
向指定时间戳添加整秒数。

以下示例返回 `"1991-01-01T00:00:00.000Z"`：

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
向指定时间戳添加整分钟数。

以下示例返回 `"1991-01-01T00:00:59.000Z"`：

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
向指定时间戳添加整小时数。

以下示例返回 `"1991-01-01T00:59:59.000Z"`：

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。

