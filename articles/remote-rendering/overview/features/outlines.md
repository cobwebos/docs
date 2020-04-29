---
title: 轮廓渲染
description: 说明如何执行选择大纲呈现
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680825"
---
# <a name="outline-rendering"></a>轮廓渲染

通过使用[层次结构状态覆盖组件](../../overview/features/override-hierarchical-state.md)添加大纲呈现，可直观地突出显示所选对象。 本章介绍了如何通过客户端 API 更改大纲呈现的全局参数。

大纲属性是全局设置。 使用大纲呈现的所有对象都将使用相同的设置-不能使用每个对象的轮廓颜色。

## <a name="parameters-for-outlinesettings"></a>的参数`OutlineSettings`

类`OutlineSettings`包含与全局大纲属性相关的设置。 它公开以下成员：

| 参数      | 类型    | 说明                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | 用于绘制轮廓的颜色。 Alpha 部分将被忽略。         |
| `PulseRateHz`    | FLOAT   | 每秒大纲振荡的速率|
| `PulseIntensity` | FLOAT   | 大纲脉冲效果的强度。 对于完全闪烁1.0，必须介于0.0 到之间。 强度隐式将大纲的最小不透明度`MinOpacity = 1.0 - PulseIntensity`设置为。 |

![概述](./media/outlines.png)将`color`参数从黄色（左）改为洋红色（中心）和`pulseIntensity`从0到0.8 （右）的效果。

## <a name="example"></a>示例

下面的代码演示了通过 API 设置大纲参数的示例：

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>性能

大纲呈现可能会对呈现性能产生重大影响。 根据给定帧的选定对象和非选定对象之间的屏幕空间关系，这种影响会有所不同。

## <a name="next-steps"></a>后续步骤

* [分层状态覆盖组件](../../overview/features/override-hierarchical-state.md)
