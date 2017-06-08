---
title: "Azure 托管应用程序 UserNameTextBox UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Compute.UserNameTextBox UI 元素"
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
ms.date: 05/12/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: c90be5a0ed3aadda81d7ec9b5388a96472f69af0
ms.contentlocale: zh-cn
ms.lasthandoff: 05/13/2017


---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI 元素
一个具有针对 Windows 和 Linux 用户名的内置验证的文本框控件。 [创建 Azure 托管应用程序](managed-application-publishing.md)时使用此元素。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>备注
- 如果 `constraints.required` 设置为 **true**，则文本框必须包含值才能成功通过验证。 默认值为 **true**。
- 必须指定 `osPlatform`，它可以是 **Windows** 或 **Linux**。
- `constraints.regex` 是一个 JavaScript 正则表达式模式。 如果指定，则文本框的值必须与模式完全匹配才能成功通过验证。 默认值为 **null**。
- `constraints.validationMessage` 是当文本框的值未通过 `constraints.regex` 指定的验证时将显示的一个字符串。 如果未指定，则会使用文本框的内置验证消息。 默认值为 **null**。
- 此元素具有基于为 `osPlatform` 指定的值的内置验证。 内置验证可以与自定义正则表达式一起使用。
如果指定了 `constraints.regex` 的值，则会同时触发内置验证和自定义验证。

## <a name="sample-output"></a>示例输出
```json
"tabrezm"
```

## <a name="next-steps"></a>后续步骤
* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](managed-application-overview.md)。
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

