---
title: 后期阶段重新投影
description: 有关延迟阶段 Reprojection 以及如何使用它的信息。
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 4aa1148e544ff3451aa1cb956bc4a5fb932b9611
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680981"
---
# <a name="late-stage-reprojection"></a>后期阶段重新投影

*延迟阶段 Reprojection* （LSR）是一项硬件功能，可帮助在用户移动时稳定影像。

当你移动静态模型时，它们应该在视觉上保持其位置。 如果它们看起来不稳定，此行为可能会在 LSR 问题上提示。 请注意，其他动态转换（如动画或分离视图）可能会掩盖此行为。

可以在两个不同的 LSR 模式（即**平面 LSR**或**深度 LSR**）之间进行选择。 哪一个处于活动状态取决于客户端应用程序是否提交深度缓冲区。

尽管这两种模式都具有不同的限制，但这两种模式都能改善全息 LSR 首先尝试使用深度 LSR，因为在大多数情况下，可能会提供更好的结果。

## <a name="choose-lsr-mode-in-unity"></a>选择 Unity 中的 LSR 模式

在 Unity 编辑器中，切换到 "*文件 > 生成设置*"。 选择左下角的 "*播放机设置*"，然后在 "*播放器 > XR 设置" > 虚拟现实 Sdk > Windows Mixed Reality* ，无论是否已选中 "**启用深度缓冲区共享**"：

![深度缓冲区共享已启用标志](./media/unity-depth-buffer-sharing-enabled.png)

如果是，你的应用将使用深度 LSR，否则将使用平面 LSR。

## <a name="depth-lsr"></a>深度 LSR

要使深度 LSR 正常工作，客户端应用程序必须提供一个有效的深度缓冲区，其中包含在 LSR 期间要考虑的所有相关几何。

深度 LSR 尝试根据提供的深度缓冲区的内容来使视频帧稳定。 因此，不能通过 LSR 调整未呈现给它的内容（如透明对象），并且可能会显示不稳定和 reprojection 项目。

## <a name="planar-lsr"></a>平面 LSR

平面 LSR 没有每像素深度信息，因为深度 LSR。 相反，它会根据必须提供每个帧的平面来 reprojects 所有内容。

平面 LSR reprojects 那些与所提供的平面最接近的对象。 对象越远离，其外观就越不稳定。 虽然在不同深度的 reprojecting 对象上，深度 LSR 更好，但平面 LSR 可以更好地与平面进行比较。

### <a name="configure-planar-lsr-in-unity"></a>在 Unity 中配置平面 LSR

平面参数派生自所谓的 "*焦点点*"，您必须通过`UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`提供每个框架。 有关详细信息，请参阅[Unity 要点 API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) 。 如果未设置焦点，则将为你选择一个回退。 但是，自动回退通常会导致不理想的结果。

您可以自行计算焦点，不过，这可能会使其基于由远程呈现主机计算的点。 调用`RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint`以获取。 系统会要求提供一个坐标框架，以便在其中表示焦点。 在大多数情况下，只需在`UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr`此处提供结果。

通常情况下，客户端和主机都会呈现另一方不知道的内容，例如客户端上的 UI 元素。 因此，将远程焦点与本地计算的焦点组合在一起可能有意义。

在两个连续的帧中计算的重点点可能会有很大不同。 只是将其按原样使用可能会导致全息影像出现。 若要防止此行为，建议在上一个和当前焦点之间进行插值。

## <a name="next-steps"></a>后续步骤

* [服务器端性能查询](performance-queries.md)
