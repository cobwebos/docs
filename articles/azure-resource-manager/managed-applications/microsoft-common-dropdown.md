---
title: DropDown UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.DropDown UI 元素。 用于在部署托管应用程序时从可用选项中进行选择。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: cc50e49d4bc59235a147d114d86ecdff95dca797
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474336"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 元素

一个包含下拉列表的选择控件。 可以允许选择一个项（单选）或多个项（多选）。 还可以选择在项中包含说明。

## <a name="ui-sample"></a>UI 示例

DropDown 元素提供不同的选项，用于确定其在门户中的视觉效果。

当只允许选择单个项时，控件将显示为：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft.Common.DropDown 单项选择":::

当包含说明时，控件将显示为：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft.Common.DropDown 单项选择":::

启用多选时，控件将添加“全选”选项和用于选择多个项目的复选框：

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft.Common.DropDown 单项选择":::

启用多选时可以包含说明。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft.Common.DropDown 单项选择":::

启用筛选后，控件包含用于添加筛选值的文本框。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft.Common.DropDown 单项选择":::

## <a name="schema"></a>架构

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
                "value": "two"
            }
        ],
        "required": true
    },
    "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
"two"
```

## <a name="remarks"></a>备注

- 使用 `multiselect` 指定用户是否可以选择多个项。
- 默认情况下，启用多选时，`selectAll` 为 `true`。
- 通过 `filter` 属性，用户能够在众多选项中进行搜索。
- `constraints.allowedValues` 的标签是项的显示文本，并且当选中时，其值是元素的输出值。
- 如果已指定，则默认值必须是 `constraints.allowedValues` 中存在的一个标签。 如果未指定，则将选择 `constraints.allowedValues` 中的第一个项。 默认值为 **null**。
- `constraints.allowedValues` 必须至少有一项。
- 若要模拟一个不需要的值，请向 `constraints.allowedValues` 添加一个具有标签且值为 `""`（空字符串）的项。
- `defaultDescription` 属性用于不带说明的项。
- `placeholder` 属性是帮助文本，当用户开始编辑后会消失。 如果同时定义了 `placeholder` 和 `defaultValue`，会优先显示 `defaultValue`。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
