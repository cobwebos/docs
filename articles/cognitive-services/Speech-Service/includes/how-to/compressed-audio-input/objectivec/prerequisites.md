---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422227"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译，也未与语音 SDK 链接。 但是，需要使用 SDK 构建包含这些函数的包装器库，并将其与应用一起提供。

若要构建该包装器库，请先下载并安装 [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然后，下载[包装器库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的 Xcode  项目。

在 Xcode  中打开此项目，针对通用 iOS 设备  目标生成它 -- 如果针对特定目标生成它，它将  无法正常运行。

构建步骤会生成一个动态框架捆绑包（其中的动态库适用于所有必需的体系结构），其名称为 `GStreamerWrapper.framework`。

此框架必须包含在所有通过语音服务 SDK 使用压缩音频流的应用中。

为此，请在 Xcode  项目中应用以下设置：

1. 将刚刚生成的 `GStreamerWrapper.framework` 和认知服务语音 SDK 的框架（可以从[此处](https://aka.ms/csspeech/iosbinary)下载）复制到包含示例项目的目录中。
1. 在“项目设置”中调整框架的路径。 
   1. 在“嵌入式二进制文件”标头下的“常规”选项卡中，添加 SDK 库作为框架   ：“添加嵌入式二进制文件” > “添加其他...”> 导航到所选目录，然后选择两个框架。   。
   1. 转到“生成设置”  选项卡，激活“所有”设置  。
1. 将目录 `$(SRCROOT)/..` 添加到“搜索路径”标头下的“框架搜索路径”。  
