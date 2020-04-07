---
title: 轮廓渲染
description: 说明如何进行选择轮廓渲染
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680825"
---
# <a name="outline-rendering"></a>轮廓渲染

通过[分层状态覆盖组件](../../overview/features/override-hierarchical-state.md)添加轮廓渲染，可以直观地突出显示选定的对象。 本章介绍如何通过客户端 API 更改大纲呈现的全局参数。

大纲属性是全局设置。 使用轮廓渲染的所有对象都将使用相同的设置 - 无法使用每个对象的轮廓颜色。

## <a name="parameters-for-outlinesettings"></a>参数`OutlineSettings`

类`OutlineSettings`包含与全局大纲属性相关的设置。 它公开以下成员：

| 参数      | 类型    | 说明                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | 颜色4Ub | 用于绘制轮廓的颜色。 将忽略 alpha 部分。         |
| `PulseRateHz`    | FLOAT   | 轮廓每秒振荡的速度|
| `PulseIntensity` | FLOAT   | 轮廓脉冲效果的强度。 无脉动必须介于 0.0 之间，完全脉动必须介于 1.0 之间。 强度隐式将轮廓的最小不一度设置`MinOpacity = 1.0 - PulseIntensity`为 。 |

![大纲](./media/outlines.png)将`color`参数从黄色（左）更改为品红色（中心）和`pulseIntensity`从 0 到 0.8（右）的效果。

## <a name="example"></a>示例

以下代码显示了通过 API 设置大纲参数的示例：

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

大纲渲染可能对渲染性能产生重大影响。 此影响因给定帧的选定对象和非选定对象之间的屏幕空间空间关系而异。

## <a name="next-steps"></a>后续步骤

* [分层状态覆盖组件](../../overview/features/override-hierarchical-state.md)
