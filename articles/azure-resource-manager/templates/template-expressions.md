---
title: 模板语法和表达式
description: 介绍 Azure 资源管理器模板的声明性 JSON 语法。
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: b7682ba10c30290e5935bc2dd17e2a83852d92f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484189"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的语法和表达式

模板的基本语法为 JSON。 但是，可以使用表达式来扩展模板内可用的 JSON 值。  表达式以括号（`[` 和 `]`）分别开始和结束。 部署模板时会计算表达式的值。 表达式可以返回字符串、整数、布尔值、数组或对象。

模板表达式不能超过24576个字符。

## <a name="use-functions"></a>使用函数

下面的示例演示参数的默认值中的表达式：

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

## <a name="next-steps"></a>后续步骤

* 有关模板函数的完整列表，请参阅 [Azure 资源管理器模板函数](template-functions.md)。
* 有关模板文件的详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
