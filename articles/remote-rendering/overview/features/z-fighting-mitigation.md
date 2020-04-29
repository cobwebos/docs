---
title: Z 冲突缓解
description: 介绍降低 z 反击项目的方法
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680448"
---
# <a name="z-fighting-mitigation"></a>Z 冲突缓解

当两个曲面重叠时，并不清楚哪个面应在另一个上呈现。 甚至每个像素都有不同的结果，导致视图相关的项目。 因此，当照相机或网格移动时，这些模式会明显闪烁。 此项目称为*z 反击*。 对于 AR 和 VR 应用程序，问题是难度的，因为安装了 head 的设备自然总是移动。 若要防止查看器 discomfort 在 Azure 远程呈现中提供 z 反击缓解功能。

## <a name="z-fighting-mitigation-modes"></a>Z 反击缓解模式

|场景                        | 结果                               |
|---------------------------------|:-------------------------------------|
|常规 z 反击               |![Z-反击](./media/zfighting-0.png)|
|已启用 Z 反击缓解    |![Z-反击](./media/zfighting-1.png)|
|已启用棋盘突出显示|![Z-反击](./media/zfighting-2.png)|

以下代码支持 z 反击缓解：

``` cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

> [!NOTE]
> Z 反击缓解是一项全局设置，它会影响所有呈现的网格。

## <a name="reasons-for-z-fighting"></a>Z 反击原因

Z 反击的发生主要有两个原因：

1. 当表面非常远离照相机时，其深度值的精度会下降，并且值变得不可区分
1. 当网格中的表面物理重叠时

第一个问题总是可以发生，并且难以消除。 如果你的应用程序中出现这种情况，请确保*接近平面*距离到*远处飞机*距离的比率尽可能低。 例如，接近0.01 和远处平面的接近于1000的一种接近平面将会更早地创建此问题，接近于0.1 的附近平面和距离为20的远端平面。

第二个问题是针对错误编写的内容的指示器。 在现实世界中，两个对象不能同时处于同一位置。 根据应用程序的不同，用户可能希望知道重叠的图面是否存在以及它们的位置。 例如，建筑物的 CAD 场景是实际建筑的基础，不能包含物理上不可能的曲面交集。 为了允许视觉检查，突出显示模式可用，该模式将可能的 z 反击显示为动画棋盘图案。

## <a name="limitations"></a>限制

提供的 z 反击缓解是最大努力。 不能保证删除所有 z。 此外，它还会将一个表面自动用于另一个表面。 因此，当您的表面太近时，可能会出现 "错误" 的表面。 一个常见的问题是，将文本和其他 decals 应用到图面。 启用 z 反击缓解后，这些详细信息很容易消失。

## <a name="performance-considerations"></a>性能注意事项

* 启用 z 反击缓解几乎不会产生性能开销。
* 此外，如果启用 z 反击覆盖，则会产生不明显的性能开销，但这可能因场景而异。

## <a name="next-steps"></a>后续步骤

* [渲染模式](../../concepts/rendering-modes.md)
* [后期阶段重新投影](late-stage-reprojection.md)
