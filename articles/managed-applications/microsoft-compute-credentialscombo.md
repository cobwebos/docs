---
title: Azure CredentialsCombo UI 元素 | Microsoft Docs
description: 介绍了 Azure 门户的 Microsoft.Compute.CredentialsCombo UI 元素。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098614"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI 元素
一组控件，其中包含针对 Windows 和 Linux 密码及 SSH 公钥的内置验证。

## <a name="ui-sample"></a>UI 示例

对于 Windows，用户可以看到：

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

对于选择了密码的 Linux，用户可以看到：

![Microsoft.Compute.CredentialsCombo Linux 密码](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

对于选择了 SSH 公钥的 Linux，用户可以看到：

![Microsoft.Compute.CredentialsCombo Linux 密钥](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>架构
对于 Windows，请使用以下架构：

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

对于 **Linux**，请使用以下架构：

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>备注
- 必须指定 `osPlatform`，它可以是 **Windows** 或 **Linux**。
- 如果 `constraints.required` 设置为 **true**，则密码或 SSH 公钥文本框中必须有值才能成功通过验证。 默认值为 **true**。
- 如果 `options.hideConfirmation` 设置为 **true**，则用于确认用户密码的第二个文本框会被隐藏。 默认值为 **false**。
- 如果 `options.hidePassword` 设置为 **true**，则使用密码身份验证这一选项会被隐藏。 只有当 `osPlatform` 为 **Linux** 时才能使用它。 默认值为 **false**。
- 可以使用 `customPasswordRegex` 属性实现对允许的密码的其他约束。 当密码没有通过自定义验证时，会显示 `customValidationMessage` 中的字符串。 两个属性的默认值都是 **null**。

## <a name="sample-output"></a>示例输出
如果 `osPlatform` 为 **Windows** 或者 `osPlatform` 为 **Linux**，并且用户提供了密码而非 SSH 公钥，则控件将返回以下输出：

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

如果 `osPlatform` 为 **Linux** 并且用户提供了 SSH 公钥，则控件将返回以下输出：

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
