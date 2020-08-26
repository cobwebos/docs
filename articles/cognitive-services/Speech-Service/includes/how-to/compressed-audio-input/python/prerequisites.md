---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282717"
---
处理压缩的音频是使用实现的 [`GStreamer`](https://gstreamer.freedesktop.org) 。 出于许可原因， `GStreamer` 不会编译二进制文件并将其与语音 SDK 关联。 开发人员需要安装几个依赖项和插件，请参阅[在 Windows 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 `GStreamer`二进制文件需要位于系统路径中，以便语音 SDK 可在运行时加载二进制文件。 如果在运行时可以找到语音 SDK `libgstreamer-1.0-0.dll` ，则意味着二进制文件在系统路径中。

