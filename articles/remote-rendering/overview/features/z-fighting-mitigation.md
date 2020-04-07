---
title: Z 战斗缓解
description: 描述缓解 z 战斗伪影的技术
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: bc06deafe3f589fce9a9178fefdb22388254929d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680448"
---
# <a name="z-fighting-mitigation"></a>Z 战斗缓解

当两个曲面重叠时，不清楚哪一个应呈现在另一个曲面的顶部。 结果甚至因像素而异，从而导致与视图相关的伪影。 因此，当摄像机或网格移动时，这些模式会明显闪烁。 此伪影称为*z 战斗*。 对于 AR 和 VR 应用，问题更加强化，因为头戴式设备自然会移动。 为了防止观看者不适，Azure 远程渲染中提供了 z 防功能缓解功能。

## <a name="z-fighting-mitigation-modes"></a>Z 防震缓解模式

|场景                        | 结果                               |
|---------------------------------|:-------------------------------------|
|定期 z 战斗               |![Z 战斗](./media/zfighting-0.png)|
|Z 战斗缓解启用    |![Z 战斗](./media/zfighting-1.png)|
|已启用棋盘突出显示|![Z 战斗](./media/zfighting-2.png)|

以下代码支持 z 防争缓解：

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
> Z 防争缓解是影响所有呈现的网件的全局设置。

## <a name="reasons-for-z-fighting"></a>z 战斗的原因

Z 战斗发生主要有两个原因：

1. 当曲面远离相机时，其深度值的精度会降低，并且这些值变得无法区分
1. 当网格中的曲面物理重叠时

第一个问题总是会发生，而且很难消除。 如果应用程序中发生这种情况，请确保*近平面*距离与*远平面*距离的比率尽可能低。 例如，距离 0.01 处的近平面和距离 1000 处的远平面将比在 0.1 处的近平面和距离 20 处的远平面更早产生此问题。

第二个问题是编写不良内容的指示器。 在现实世界中，两个对象不能同时位于同一位置。 根据应用程序的不同，用户可能想知道是否存在重叠曲面及其位置。 例如，作为真实世界结构基础的建筑物的 CAD 场景不应包含物理上不可能的表面交点。 为了便于目视检查，突出显示模式可用，该模式将潜在的 z 战斗显示为动画棋盘图案。

## <a name="limitations"></a>限制

提供的 z 战斗缓解是最大的努力。 不能保证它消除所有 z 战斗。 此外，它会自动首选一个曲面而不是另一个曲面。 因此，当您的曲面彼此太近时，可能会出现"错误"曲面最终位于顶部。 一个常见的问题是文本和其他贴图应用于曲面时。 启用 z 战斗缓解功能后，这些细节很容易消失。

## <a name="performance-considerations"></a>性能注意事项

* 启用 z 防功能缓解只需很少或根本没有性能开销。
* 此外，启用 z 战斗叠加确实会产生非同寻常的性能开销，尽管它可能因场景而异。

## <a name="next-steps"></a>后续步骤

* [渲染模式](../../concepts/rendering-modes.md)
* [后期重新投影](late-stage-reprojection.md)
