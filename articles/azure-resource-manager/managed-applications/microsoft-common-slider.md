---
title: 滑块 UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.Slider UI 元素。 允许用户从一系列选项中设置值。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096336"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft.Common.Slider UI 元素

“滑块”控件允许用户从一系列允许的值中进行选择。

## <a name="ui-sample"></a>UI 示例

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft.Common.Slider":::

## <a name="schema"></a>架构

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
26
```

## <a name="remarks"></a>备注

- `min` 和 `max` 值是必需的。 它们设置滑块的起点和终点。
- `showStepMarkers` 属性默认值为真。 仅当最小值到最大值的范围小于等于 100 时，才会显示步长标记。


## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
