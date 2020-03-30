---
title: 模板语法和表达式
description: 介绍 Azure 资源管理器模板的声明性 JSON 语法。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460103"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的语法和表达式

模板的基本语法为 JSON。 但是，可以使用表达式来扩展模板中可用的 JSON 值。  表达式分别以方括号 `[` 与 `]` 开头和结尾。 部署模板时会计算表达式的值。 表达式可以返回字符串、整数、布尔值、数组或对象。

模板表达式不能超过 24,576 个字符。

## <a name="use-functions"></a>使用函数

Azure[资源管理器提供了可在](template-functions.md)模板中使用的功能。 下面的示例显示了在参数的默认值中使用函数的表达式：

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

在该表达式中，语法 `resourceGroup()` 调用资源管理器提供的、在模板中使用的某个函数。 在本例中，它是 [resourceGroup](template-functions-resource.md#resourcegroup) 函数。 如同在 JavaScript 中一样，函数调用的格式为 `functionName(arg1,arg2,arg3)`。 语法 `.location` 从该函数返回的对象中检索一个属性。

模板函数及其参数不区分大小写。 例如，Resource Manager 将 **variables('var1')** 和 **VARIABLES('VAR1')** 视为相同。 在求值时，除非函数明确修改大小写（例如，使用 toUpper 或 toLower 进行修改），否则函数将保留大小写。 某些资源类型可能有独立于函数求值方式的大小写要求。

若要将字符串值作为参数传递给函数，请使用单引号。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

无论部署到资源组、订阅、管理组还是租户，大多数函数的工作方式都相同。 以下函数具有基于范围的限制：

* [资源组](template-functions-resource.md#resourcegroup)- 只能在部署到资源组时使用。
* [resourceId](template-functions-resource.md#resourceid) - 可在任何作用域使用，但有效参数会根据范围而变化。
* [订阅](template-functions-resource.md#subscription)- 只能在部署到资源组或订阅时使用。

## <a name="escape-characters"></a>转义字符

要使文本字符串以左方括号 `[` 开头，以右方括号 `]` 结尾，但不将其解释为表达式，请添加额外的方括号使字符串以 `[[` 开头。 例如，变量：

```json
"demoVar1": "[[test value]"
```

解析为 `[test value]`。

但是，如果文本字符串没有以方括号结束，则不要转义第一个方括号。 例如，变量：

```json
"demoVar2": "[test] value"
```

解析为 `[test] value`。

若要转义表达式中的双引号（例如，在模板中添加 JSON 对象），请使用反斜杠。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

传入参数值时，转义字符的使用取决于参数值的指定位置。 如果在模板中设置了默认值，则需要额外的左括号。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

如果使用默认值，模板将返回`[test value]`。

但是，如果将参数值传递到命令行，则字符将字面解释。 使用以下功能部署以前的模板：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

返回 `[[test value]`。 相反，请使用：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

从参数文件传入值时，也适用相同的格式。 字符被字面解释。 当与前面的模板一起使用时，以下参数文件返回`[test value]`：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null 值

要将属性设置为 null，可以使用**null**或 **[json（"null"）]**。 当您作为参数提供`null`时[，json 函数](template-functions-array.md#json)将返回一个空对象。 在这两种情况下，资源管理器模板都将其视为属性不存在。

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>后续步骤

* 有关模板函数的完整列表，请参阅 [Azure 资源管理器模板函数](template-functions.md)。
* 有关模板文件的详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
