---
title: 在 Unity 中使用全息远程处理和远程渲染
description: 如何结合使用 Azure 远程渲染来使用全息远程处理预览
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: 180af30f57a8123b6e90cc8b11848b92b3c86db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802168"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>在 Unity 中使用全息远程处理和远程渲染

[全息远程处理](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) 和 Azure 远程呈现在一个应用程序中互相排斥。 同样，也无法使用 [Unity 播放模式](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) 。

对于每次运行 Unity 编辑器，只能使用其中一种。 若要使用其他帐户，请先重新启动 Unity。

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>使用 Unity 播放模式在 HoloLens 2 上预览

 仍可使用 Unity 播放模式，例如，用于测试应用程序的 UI。 不过，这一点非常重要。 否则，会发生崩溃。

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>使用 WMR VR 耳机在桌面上预览

如果存在 Windows Mixed Reality VR 耳机，可用于在 Unity 内预览。 在这种情况下，可以初始化 ARR，但在使用 WMR 耳机时，将无法连接到会话。
