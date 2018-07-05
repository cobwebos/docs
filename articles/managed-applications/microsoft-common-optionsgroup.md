---
title: Azure OptionsGroup UI 元素 | Microsoft Docs
description: 介绍了 Azure 门户的 Microsoft.Common.OptionsGroup UI 元素。
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
ms.openlocfilehash: e51061dc781e4ec6e822cde9cc450887ff3b1368
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061179"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI 元素
一个具有一行可用选项的选择控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- `constraints.allowedValues` 的标签是项的显示文本，并且当选中时，其值是元素的输出值。
- 如果已指定，则默认值必须是 `constraints.allowedValues` 中存在的一个标签。 如果未指定，则默认情况下将选择 `constraints.allowedValues` 中的第一个项。 默认值为 **null**。
- `constraints.allowedValues` 必须至少有一项。

## <a name="sample-output"></a>示例输出
```json
"two"
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
