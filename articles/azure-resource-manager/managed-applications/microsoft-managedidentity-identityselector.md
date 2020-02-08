---
title: IdentitySelector UI 元素
description: 描述 Azure 门户的对 microsoft.managedidentity. IdentitySelector UI 元素。 使用将托管标识分配给资源。
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087539"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>对 microsoft.managedidentity. IdentitySelector UI 元素

用于为部署中的资源分配[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的控件。

## <a name="ui-sample"></a>UI 示例

控件包含以下元素：

![对 microsoft.managedidentity. IdentitySelector 第一步](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

当用户选择 "**添加**" 时，将打开以下窗体。 用户可以为资源选择一个或多个用户分配的标识。

![对 microsoft.managedidentity. IdentitySelector 第二步](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

选定的标识显示在表中。 用户可以添加或删除此表中的项。

![对 microsoft.managedidentity. IdentitySelector 第三步](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

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

- 使用**systemAssignedIdentity**为系统分配的标识选项控件设置初始值。 默认值为**Off**。 允许以下值：
  - **启用**–分配给资源的系统分配的标识。
  - **关闭**–未将系统分配的标识分配给资源。
  - **OnOnly** –分配给资源的系统分配的标识。 在部署期间，用户无法编辑此值。
  - **OffOnly** –未将系统分配的标识分配给资源。 在部署期间，用户无法编辑此值。

- 如果**hideSystemAssignedIdentity**设置为**true**，则不会显示用于配置系统分配标识的 UI。 此选项的默认值为**false**。
- 如果**hideUserAssignedIdentity**设置为**true**，则不显示配置用户分配的标识的 UI。 未向资源分配用户分配的标识。 此选项的默认值为**false**。

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。