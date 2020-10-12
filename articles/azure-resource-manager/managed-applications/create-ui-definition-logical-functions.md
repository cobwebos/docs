---
title: 创建 UI 定义逻辑函数
description: 介绍用于执行逻辑运算的函数。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095978"
---
# <a name="createuidefinition-logical-functions"></a>创建 UI 定义逻辑函数

可以在条件表达式中使用这些函数。 某些函数可能不支持所有的 JSON 数据类型。

## <a name="and"></a>and

如果所有参数的计算结果都为 `true`，则返回 `true`。 此函数仅支持布尔类型的两个或两个以上参数。

以下示例返回 `true`：

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

以下示例返回 `false`：

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

返回第一个非 null 参数的值。 此函数支持所有 JSON 数据类型。

假设未定义 `element1` 和 `element2`。 以下示例返回 `"Contoso"`：

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

在页面加载后因用户操作而发生可选调用的情况下，此功能特别有用。 例如，UI 的一个字段中的约束取决于最初不可见的另一个字段中的当前选定值时。 在这种情况下，可以使用 `coalesce()` 使该函数在页面加载时在语法上有效，同时使用户与该字段进行交互时获得所需的效果。

请考虑使用此 `DropDown`，以允许用户在几种不同的数据库类型中进行选择：

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

若要限制另一个字段对此字段的当前选定值进行操作，请使用 `coalesce()`，如下所示：

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

这是必需的，因为 `databaseType` 最初不可见，所以没有值。 这将导致整个表达式无法正确求值。

## <a name="equals"></a>equals

如果两个参数具有相同的类型和值，则返回 `true`。 此函数支持所有 JSON 数据类型。

以下示例返回 `true`：

```json
"[equals(0, 0)]"
```

以下示例返回 `true`：

```json
"[equals('web', 'web')]"
```

以下示例返回 `false`：

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

如果第一个参数严格大于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `false`：

```json
"[greater(1, 2)]"
```

以下示例返回 `true`：

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

如果第一个参数大于或等于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `true`：

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

根据条件为 true 或 false 返回值。 第一个参数是要测试的条件。 第二个参数是条件为 true 时返回的值。 第三个参数是条件为 false 时返回的值。

下面的示例返回 `yes`。

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

如果第一个参数严格小于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `true`：

```json
"[less(1, 2)]"
```

以下示例返回 `false`：

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

如果第一个参数小于或等于第二个参数，则返回 `true`。 此函数仅支持数字和字符串类型的参数。

以下示例返回 `true`：

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

如果参数的计算结果为 `false`，则返回 `true`。 此函数仅支持布尔类型的参数。

以下示例返回 `true`：

```json
"[not(false)]"
```

以下示例返回 `false`：

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>或

如果至少有一结个参数的计算果为 `true`，则返回 `true`。 此函数仅支持布尔类型的两个或两个以上参数。

以下示例返回 `true`：

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

以下示例返回 `true`：

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。
