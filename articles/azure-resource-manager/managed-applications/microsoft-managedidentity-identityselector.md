---
title: 标识选择者 UI 元素
description: 描述 Azure 门户的 Microsoft.托管标识.标识选择或 UI 元素。 用于将托管标识分配给资源。
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087539"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>微软.托管标识.身份选择者 UI 元素

用于为部署中的资源分配[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的控件。

## <a name="ui-sample"></a>UI 示例

该控件由以下元素组成：

![微软.托管身份.身份选择者第一步](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

当用户选择 **"添加**"时，将打开以下窗体。 用户可以为资源选择一个或多个用户分配的身份。

![微软.托管身份.身份选择或第二步](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

所选标识显示在表中。 用户可以从此表中添加或删除项目。

![微软.托管身份.身份选择或第三步](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>架构

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>备注

- 使用**defaultValue.system 分配标识**为系统分配的标识选项控件设置初始值。 默认值为 **"关闭**"。 允许以下值：
  - **打开**= 系统分配的标识分配给资源。
  - **关闭**= 系统分配的标识未分配给资源。
  - **仅限 =** 将系统分配的标识分配给资源。 在部署期间，用户无法编辑此值。
  - **仅**关闭 - 未将系统分配的标识分配给资源。 在部署期间，用户无法编辑此值。

- 如果**选项.hideSystem 分配标识**设置为**true，** 则不显示用于配置系统分配标识的 UI。 此选项的默认值**为 false**。
- 如果**选项.hideUser 分配标识**设置为**true，** 则不显示用于配置用户分配标识的 UI。 未为资源分配用户分配的标识。 此选项的默认值**为 false**。

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。