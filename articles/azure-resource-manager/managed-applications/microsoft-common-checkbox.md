---
title: CheckBox UI 元素
description: 描述 Azure 门户的 "Microsoft 通用" 复选框 UI 元素。 允许用户选择选中或取消选中某个选项。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096340"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft Common. CheckBox UI 元素

CheckBox 控件允许用户选中或取消选中某个选项。 选中控件时，控件将返回**true** ; 如果未选中，则返回**false** 。

## <a name="ui-sample"></a>UI 示例

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

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

如果将 "**必需**" 设置为 " **true**"，则用户必须选中该复选框。 如果用户未选中该复选框，则显示验证消息。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
