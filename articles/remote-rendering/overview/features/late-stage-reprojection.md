---
title: 后期重新投影
description: 有关后期重新投影以及如何使用它的信息。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680981"
---
# <a name="late-stage-reprojection"></a>后期重新投影

*后期重投影*（LSR） 是一种硬件功能，可帮助在用户移动时稳定全息图。

静态模型在移动时应直观地保持其位置。 如果它们看起来不稳定，则此行为可能会提示 LSR 问题。 请注意，其他动态转换（如动画或分解视图）可能会掩盖此行为。

您可以在两种不同的 LSR 模式之间进行选择，即**平面 LSR**或**深度 LSR**。 哪个处于活动状态取决于客户端应用程序是否提交深度缓冲区。

这两种 LSR 模式都提高了全息图的稳定性，尽管它们有其明显的局限性。 首先尝试深度 LSR，因为它可以说在大多数情况下会提供更好的结果。

## <a name="choose-lsr-mode-in-unity"></a>在统一中选择 LSR 模式

在 Unity 编辑器中，转到 *">生成设置的文件*"。 选择左下角的*播放器设置*，然后在 *"播放器> XR 设置>虚拟现实 SDK > Windows 混合现实*中检查是否选中**启用深度缓冲区共享**：

![深度缓冲区共享启用标志](./media/unity-depth-buffer-sharing-enabled.png)

如果是，你的应用将使用深度 LSR，否则将使用平面 LSR。

## <a name="depth-lsr"></a>深度 LSR

要使深度 LSR 正常工作，客户端应用程序必须提供有效的深度缓冲区，其中包含在 LSR 期间要考虑的所有相关几何体。

深度 LSR 尝试根据提供的深度缓冲区的内容稳定视频帧。 因此，尚未呈现给它的内容（如透明对象）无法由 LSR 进行调整，并且可能会显示不稳定和重新投影伪影。

## <a name="planar-lsr"></a>平面 LSR

平面 LSR 没有每像素深度信息，深度 LSR 也是如此。 相反，它根据必须为每个帧提供的平面重新项目所有内容。

平面 LSR 会重新投射那些最靠近提供的平面的对象。 物体越远，它看起来就越不稳定。 虽然深度 LSR 更善于在不同深度处重新投影对象，但平面 LSR 可能更适合与平面对齐的内容。

### <a name="configure-planar-lsr-in-unity"></a>统一配置平面 LSR

平面参数派生自所谓的*焦点*，您必须通过`UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`提供每个帧。 有关详细信息，请参阅[统一焦点 API。](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 如果不设置焦点，将为您选择回退。 但是，自动回退通常会导致结果不理想。

您可以自己计算焦点，尽管将其基于远程渲染主机计算的焦点可能有意义。 调用`RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint`以获取该。 系统要求您提供一个坐标框架，用于表达焦点。 在大多数情况下，您只想在此处`UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr`提供结果。

通常，客户端和主机都呈现对方不知道的内容，例如客户端上的 UI 元素。 因此，将远程焦点与本地计算的焦点组合起来可能有意义。

在连续两个帧中计算的焦点可能大不相同。 简单地使用它们作为可以导致全息图似乎跳跃。 为了防止此行为，建议在前一个焦点和当前焦点之间插值。

## <a name="next-steps"></a>后续步骤

* [服务器端性能查询](performance-queries.md)
