---
title: 统一使用全息调整和远程渲染
description: 如何将全息远程渲染预览与 Azure 远程渲染结合使用
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681202"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>统一使用全息调整和远程渲染

[全息远程渲染](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player)和 Azure 远程渲染在一个应用程序中是互斥的。 因此[，Unity 播放模式](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode)也不可用。

对于 Unity 编辑器的每个运行，只能使用两个编辑器中的一个。 要使用另一个，请首先重新启动 Unity。

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>使用 Unity 播放模式在 Hololens 2 上预览

 Unity 播放模式仍可以使用，例如用于测试应用程序的 UI。 但是，始终不初始化 ARR 至关重要。 否则，它会崩溃。

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>使用 WMR VR 耳机在桌面上预览

如果存在 Windows 混合现实 VR 耳机，则可用于在 Unity 中预览。 在这种情况下，可以初始化 ARR，但在使用 WMR 耳机时无法连接到会话。
