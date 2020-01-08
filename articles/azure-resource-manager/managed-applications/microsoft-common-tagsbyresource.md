---
title: TagsByResource UI 元素
description: 描述 Azure 门户的 TagsByResource UI 元素。 用于在部署期间将标记应用到资源。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652198"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>TagsByResource UI 元素

用于将[标记](../management/tag-resources.md)与部署中的资源相关联的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>备注

- 必须至少指定 `resources` 数组中的一项。
- `resources` 中的每个元素都必须是完全限定的资源类型。 这些元素将显示在 "**资源**" 下拉列表中，并由用户 taggable。
- 将对控件的输出进行格式设置，以便在 Azure 资源管理器模板中轻松分配标记值。 若要在模板中接收控件的输出，请在模板中包括参数，如以下示例中所示：

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  对于可以标记的每个资源，将标记属性分配给该资源类型的参数值：

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- 在访问 tagsByResource 参数时使用[if](../templates/template-functions-logical.md#if)函数。 当没有任何标记分配给给定的资源类型时，可以使用它来分配空的对象。

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
