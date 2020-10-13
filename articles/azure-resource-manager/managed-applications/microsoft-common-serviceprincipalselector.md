---
title: ServicePrincipalSelector UI 元素
description: 描述 Azure 门户的 ServicePrincipalSelector UI 元素。 提供一个下拉列表，用于选择应用程序标识符和文本框，以输入密码或证书指纹。
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 73b242754bfae53b6df5abd9c2c8dee33b973dad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575990"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>ServicePrincipalSelector UI 元素

允许用户选择现有服务主体或注册新服务主体的控件。 选择 " **新建**" 时，将执行注册新应用程序的步骤。 选择现有应用程序时，控件将提供一个文本框来输入密码或证书指纹。

## <a name="ui-sample"></a>UI 示例

默认视图由属性中的值确定 `defaultValue` 。 如果 `principalId` 属性包含有效的全局唯一标识符 (GUID) ，则控件会搜索应用程序的对象 ID。 如果用户没有从下拉列表中进行选择，则会应用默认值。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="ServicePrincipalSelector 初始视图&quot;:::

从下拉列表中选择 &quot; **新建** &quot; 或 &quot;现有应用程序标识符&quot; 时，将显示 " **身份验证类型** "，以在文本框中输入密码或证书指纹。

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="ServicePrincipalSelector 初始视图&quot;:::

从下拉列表中选择 &quot; **新建** &quot; 或 &quot;现有应用程序标识符&quot; 时，将显示 ":::

## <a name="schema"></a>架构

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注

- 必需的属性包括：
  - `name`
  - `type`
  - `label`
  - `defaultValue`：指定默认 `principalId` 和 `name` 。

- 可选属性包括：
  - `toolTip`：将工具提示附加 `infoBalloon` 到每个标签。
  - `visible`：隐藏或显示控件。
  - `options`：指定是否应使证书指纹选项可用。
  - `constraints`：用于密码验证的 Regex 约束。

## <a name="example"></a>示例

下面是控件的一个示例 `Microsoft.Common.ServicePrincipalSelector` 。 `defaultValue`属性将设置 `principalId` 为 `<default guid>` 默认应用程序标识符 GUID 的占位符。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>示例输出

`appId`是你选择或创建的应用程序注册的 Id。 `objectId`是为所选应用程序注册配置的服务主体的 objectIds 数组。

如果未从下拉列表中进行选择，则 `newOrExisting` 属性值为 **new**：

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": ["<default guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

从下拉列表中选择 " **新建** " 或 "现有应用程序标识符" 时， `newOrExisting` 属性值为 " **现有**"：

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": ["<guid>"]
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
