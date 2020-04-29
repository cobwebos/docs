---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421675"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译，也未与语音 SDK 链接。 开发人员需要安装几个依赖项和插件，请参阅[在 Windows 上安装](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)。 适用 gstreamer 二进制文件需要位于系统路径中，以便语音 SDK 可在运行时加载适用 gstreamer 二进制文件。 如果语音 SDK 在运行时能够找到 libgstreamer-1.0 旁1/-0，则意味着适用 gstreamer 二进制文件在系统路径中。

