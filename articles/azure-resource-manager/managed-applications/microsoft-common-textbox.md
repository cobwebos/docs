---
title: TextBox UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.TextBox UI 元素。 用于添加无格式文本。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652276"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 元素

一个可用来编辑无格式文本的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
"my text value"
```

## <a name="remarks"></a>备注

- 如果 `constraints.required` 设置为 true，则文本框必须包含值才能成功通过验证。 默认值是 **false**秒。
- `constraints.regex` 是一个 JavaScript 正则表达式模式。 如果指定，则文本框的值必须与模式完全匹配才能成功通过验证。 默认值为 **null**。
- `constraints.validationMessage` 是当文本框的值未通过验证时会显示的一个字符串。 如果未指定，则会使用文本框的内置验证消息。 默认值为 **null**。
- 当 `constraints.required` 设置为 **false** 时可以为 `constraints.regex` 指定值。 在这种情况下，文本框并非必须具有值才能成功通过验证。 如果指定了一个值，则它必须与正则表达式模式匹配。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
