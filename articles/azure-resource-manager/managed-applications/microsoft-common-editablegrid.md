---
title: EditableGrid UI 元素
description: 描述 Azure 门户的 EditableGrid UI 元素。 使用户能够收集表格输入。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893681"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>EditableGrid UI 元素

用于收集表格输入的控件。 网格中的所有字段都是可编辑的，行数可能会有所不同。

## <a name="ui-sample"></a>UI 示例

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="EditableGrid":::

## <a name="schema"></a>架构

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>注解

- 列数组中唯一有效的控件是 [TextBox](microsoft-common-textbox.md)、 [microsoft.common.optionsgroup](microsoft-common-optionsgroup.md)和 [DropDown](microsoft-common-dropdown.md)。
- `$rowIndex`变量仅在网格列的子级中包含的表达式中有效。 它是表示元素的相对行索引的整数，从1开始，以1为增量递增。 如架构的部分所示 `"columns":` ， `$rowIndex` 用于验证。
- 使用变量执行验证时 `$rowIndex` ，可以通过组合和命令来获取当前行的值 `last()` `take()` 。

  例如：

  `last(take(<reference_to_grid>, $rowIndex))`

- `label`属性不会显示为控件的一部分，而是显示在最后一个选项卡摘要上。
- 该 `ariaLabel` 属性是网格的可访问性标签。 为使用屏幕阅读器的用户指定有用的文本。
- `constraints.width`属性用于设置网格的整体宽度。 选项为 " _Full_"、" _中_"、" _小_"。 默认值为 _Full_。
- `width`列子级上的属性确定列宽。 宽度是使用小数单位（如 _3fr_）指定的，并且将总空间分配给按其单位的列。 如果未指定列宽，则默认值为 _1fr_。

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
