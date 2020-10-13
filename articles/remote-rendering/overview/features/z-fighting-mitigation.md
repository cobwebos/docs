---
title: Z 冲突缓解
description: 介绍了缓解 z 冲突现象的方法
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e217676f5e1f4dcba24917cb140d9d4d8fcc422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90024360"
---
# <a name="z-fighting-mitigation"></a>Z 冲突缓解

当两个曲面重叠时，不好确定哪个曲面应当渲染在另一个曲面顶部。 甚至每个像素的结果都是不同的，这会导致与相机视图相关的项目。 因此，当相机或网格移动时，这些模式会明显闪烁。 这种现象称为 z 冲突。 对于 AR 和 VR 应用程序，此问题进一步加剧，因为头戴式设备总是自然而然地移动。 为了防止观看者出现不适，Azure 远程渲染中提供了 z 冲突缓解功能。

## <a name="z-fighting-mitigation-modes"></a>z 冲突缓解模式

|情形                        | 结果                               |
|---------------------------------|:-------------------------------------|
|常规 z 冲突               |![红色和绿色四边形之间没有确定性的优先级](./media/zfighting-0.png)|
|已启用 Z 冲突缓解    |![红色四个优先顺序](./media/zfighting-1.png)|
|已启用棋盘格突出显示|![棋盘模式的红色和绿色四核切换首选项](./media/zfighting-2.png)|

以下代码启用 z 冲突缓解：

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Actions()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> Z 冲突缓解是一个全局设置，会影响渲染的所有网格。

## <a name="reasons-for-z-fighting"></a>Z 冲突的原因

有两个主要原因导致发生 Z 冲突：

1. 当曲面与相机之间的距离很远时，其深度值的精度会下降，并且值会变得不可辨别
1. 当网格中的曲面在物理上重叠时

第一个问题始终会发生，且难以消除。 如果你的应用程序中出现这种情况，请确保近端平面距离与远端平面距离的比率尽可能低 。 例如，与近端平面位于距离 0.1 处且远端平面位于距离 20 处的情况相比，近端平面位于距离 0.01 处且远端平面位于距离 1000 处的情况会更早地造成此问题。

第二个问题表明创作的内容不好。 在现实世界中，两个对象不能同时位于同一位置。 根据具体的应用程序，用户可能想要知道是否存在重叠的曲面以及这些曲面的位置。 例如，用作实际建筑基础的建筑物 CAD 场景不应包含实际上不可能出现的曲面相交。 若要进行视觉检查，可以使用突出显示模式，该模式会将潜在的 z 冲突显示为动画棋盘格图案。

## <a name="limitations"></a>限制

提供的 z 冲突缓解措施采用“尽力而为”的原则。 无法保证消除所有 z 冲突。 此外，此缓解措施会优先照顾两个曲面中的一个。 因此，如果曲面相互过于靠近，则最终可能会在顶层放置“错误的”曲面。 将文本和其他贴花图案应用于曲面时，经常发生此问题。 启用 z 冲突缓解后，这些细节很容易消失。

## <a name="performance-considerations"></a>性能注意事项

* 启用 z 冲突缓解几乎不会产生任何性能开销。
* 此外，启用 z 冲突覆盖会产生相当高的性能开销，不过，具体的开销因场景而异。

## <a name="api-documentation"></a>API 文档

* [C # RemoteManager ZFightingMitigationSettings 属性](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.zfightingmitigationsettings)
* [C + + RemoteManager：： ZFightingMitigationSettings ( # B1 ](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#zfightingmitigationsettings)

## <a name="next-steps"></a>后续步骤

* [渲染模式](../../concepts/rendering-modes.md)
* [后期阶段重新投影](late-stage-reprojection.md)
