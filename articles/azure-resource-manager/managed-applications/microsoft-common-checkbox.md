---
title: CheckBox UI 元素
description: 介绍 Azure 门户的 Microsoft.Common.CheckBox UI 元素。 允许用户选择检查或不检查某选项。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096340"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft.Common.CheckBox UI 元素

CheckBox 控件允许用户检查或不检查某选项。 检查了控件后，控件返回“true”，未检查时返回“false” 。

## <a name="ui-sample"></a>UI 示例

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft.Common.CheckBox":::

## <a name="schema"></a>架构

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>示例输出

```json
true
```

## <a name="remarks"></a>备注

将“必需”设置为“true”时，用户必须选中该复选框 。 如果用户未选中该复选框，会显示验证消息。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
