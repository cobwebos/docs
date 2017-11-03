---
title: "Azure 托管应用程序 CredentialsCombo UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Compute.CredentialsCombo UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: d8faa36aca762bc8d787d5750fcf7efdbaf986ea
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI 元素
一组控件，其中包含针对 Windows 和 Linux 密码及 SSH 公钥的内置验证。 [创建 Azure 托管应用程序](publish-service-catalog-app.md)时使用此元素。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>架构
如果 `osPlatform` 为 **Windows**，则使用以下架构：
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

如果 `osPlatform` 为 **Linux**，则使用以下架构：
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
- 如果 `constraints.required` 设置为 **true**，则密码或 SSH 公钥文本框中必须包含值才能成功通过验证。 默认值为 **true**。
- 如果 `options.hideConfirmation` 设置为 **true**，则用于确认用户密码的第二个文本框会被隐藏。 默认值为 **false**。
- 如果 `options.hidePassword` 设置为 **true**，则使用密码身份验证这一选项会被隐藏。 只有当 `osPlatform` 为 **Linux** 时才能使用它。 默认值为 **false**。
- 可以使用 `customPasswordRegex` 属性实现对允许的密码的其他约束。 当密码没有通过自定义验证时，会显示 `customValidationMessage` 中的字符串。 两个属性的默认值都是 **null**。

## <a name="sample-output"></a>示例输出
如果 `osPlatform` 为 **Windows**，或者用户提供了密码而非 SSH 公钥，则应当会生成以下输出：

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

如果用户提供了 SSH 公钥，则应当会生成以下输出：
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>后续步骤
* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
