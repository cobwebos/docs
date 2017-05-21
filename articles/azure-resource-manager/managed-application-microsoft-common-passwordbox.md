---
title: "Azure 托管应用程序 PasswordBox UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Common.PasswordBox UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4646521531e839e4751b4f01f7a66876dac96b57
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI 元素
一个可以用来提供和确认密码的控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

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
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- 此元素不支持 `defaultValue` 属性。
- 有关 `constraints` 的实现详细信息，请参阅 [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)。
- 如果 `options.hideConfirmation` 设置为 **true**，则用于确认用户密码的第二个文本框将被隐藏。 默认值为 **false**。

## <a name="sample-output"></a>示例输出
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

