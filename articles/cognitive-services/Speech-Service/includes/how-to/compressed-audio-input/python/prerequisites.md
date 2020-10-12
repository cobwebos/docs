---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282717"
---
处理压缩音频是使用 [`GStreamer`](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，`GStreamer` 二进制文件未编译，也未与语音 SDK 链接。 开发人员需要安装几个依赖项和插件，请参阅[在 Windows 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 `GStreamer` 二进制文件需要在系统路径中，以便语音 SDK 可以在运行时加载二进制文件。 如果语音 SDK 能够在运行时找到 `libgstreamer-1.0-0.dll`，这意味着二进制文件在系统路径中。

