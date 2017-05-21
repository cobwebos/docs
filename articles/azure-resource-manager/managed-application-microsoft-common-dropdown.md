---
title: "Azure 托管应用程序 DropDown UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Common.DropDown UI 元素"
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
ms.openlocfilehash: 14809bd7ce5eec140929de88e525df0ca7442869
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 元素
一个包含下拉列表的选择控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- `constraints.allowedValues` 的标签是项的显示文本，并且当选中时，其值是元素的输出值。
- 如果已指定，则默认值必须是 `constraints.allowedValues` 中存在的一个标签。 如果未指定，则将选择 `constraints.allowedValues` 中的第一个项。 默认值为 **null**。
- `constraints.allowedValues` 必须至少包含一个项。
- 此元素不支持 `constraints.required` 属性。 若要模拟此行为，请向 `constraints.allowedValues` 中添加一个具有标签且值为 `""`（空字符串）的项。

## <a name="sample-output"></a>示例输出
```json
"Bar"
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

