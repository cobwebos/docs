---
title: PasswordBox UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.PasswordBox UI 元素。 使用户能够在部署托管应用程序时提供机密值。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7902ea2e30dec20e57d88344dc9507aec3993600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87064082"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI 元素

一个可以用来提供和确认密码的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft-common-passwordbox.png)

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
"p4ssw0rd"
```

## <a name="remarks"></a>备注

- 此元素不支持 `defaultValue` 属性。
- 有关 `constraints` 的实现详细信息，请参阅 [Microsoft.Common.TextBox](microsoft-common-textbox.md)。
- 如果 `options.hideConfirmation` 设置为 **true**，则用于确认用户密码的第二个文本框会被隐藏。 默认值是 **false**秒。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
