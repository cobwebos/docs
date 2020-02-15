---
title: 模板语法和表达式
description: 介绍 Azure 资源管理器模板的声明性 JSON 语法。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207394"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的语法和表达式

模板的基本语法为 JSON。 但是，可以使用表达式来扩展模板内可用的 JSON 值。  表达式以方括号开头和结尾：分别为 `[` 和 `]`。 部署模板时会计算表达式的值。 表达式可以返回字符串、整数、布尔值、数组或对象。

模板表达式不能超过24576个字符。

## <a name="use-functions"></a>使用函数

Azure 资源管理器提供可在模板中使用的[函数](template-functions.md)。 下面的示例显示一个表达式，该表达式在参数的默认值中使用函数：

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

在表达式中，语法 `resourceGroup()` 调用资源管理器提供以便在模板中使用的函数之一。 在这种情况下，它是[资源](template-functions-resource.md#resourcegroup)组函数。 如同在 JavaScript 中一样，函数调用的格式为 `functionName(arg1,arg2,arg3)`。 语法 `.location` 从该函数返回的对象中检索一个属性。

模板函数及其参数不区分大小写。 例如，Resource Manager 将 **variables('var1')** 和 **VARIABLES('VAR1')** 视为相同。 在求值时，除非函数明确修改大小写（例如，使用 toUpper 或 toLower 进行修改），否则函数将保留大小写。 某些资源类型可能有与计算函数不同的大小写要求。

若要将字符串值作为参数传递给函数，请使用单引号。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

无论部署到资源组、订阅、管理组还是租户，大多数函数的工作方式都相同。 以下函数基于作用域的限制：

* [resourceGroup](template-functions-resource.md#resourcegroup) -只能在资源组的部署中使用。
* [resourceId](template-functions-resource.md#resourceid) -可以在任何范围内使用，但有效参数会根据范围而发生变化。
* [订阅](template-functions-resource.md#subscription)-只能用于部署到资源组或订阅。

## <a name="escape-characters"></a>转义字符

若要使文本字符串以左方括号开头 `[` 并以右括号结束 `]`，但不将其解释为表达式，请添加一个额外方括号以使用 `[[`启动字符串。 例如，变量：

```json
"demoVar1": "[[test value]"
```

解析为 `[test value]`。

但是，如果文本字符串不以方括号结束，则不要将第一个方括号转义。 例如，变量：

```json
"demoVar2": "[test] value"
```

解析为 `[test] value`。

若要在表达式中转义双引号（如在模板中添加 JSON 对象），请使用反斜杠。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="null-values"></a>Null 值

若要将属性设置为 null，可以使用**null**或 **[json （' null '）]** 。 作为参数提供 `null` 时， [json 函数](template-functions-array.md#json)返回一个空对象。 在这两种情况下，资源管理器模板会将其视为不存在属性。

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>后续步骤

* 有关模板函数的完整列表，请参阅 [Azure 资源管理器模板函数](template-functions.md)。
* 有关模板文件的详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
