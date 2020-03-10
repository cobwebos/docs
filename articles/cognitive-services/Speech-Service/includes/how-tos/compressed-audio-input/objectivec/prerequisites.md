---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943828"
---
处理压缩音频是使用[适用 gstreamer](https://gstreamer.freedesktop.org)实现的。 出于许可原因，适用 gstreamer 二进制文件不会进行编译和与语音 SDK 关联。 而是需要使用 SDK 生成包含这些函数的包装库，并随应用一起提供。

若要生成此包装库，请首先下载并安装[适用 GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然后，下载[包装库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的**Xcode**项目。

在**Xcode**中打开项目，并为**通用 iOS 设备**目标生成该项目--对于特定目标，它将*不*起作用。

生成步骤将生成动态框架包，其中包含具有 `GStreamerWrapper.framework`名称的所有必需体系结构的动态库。

此框架必须包含在通过语音服务 SDK 使用压缩音频流的所有应用中。

若要完成此操作，请在**Xcode**项目中应用以下设置：

1. 将刚构建的 `GStreamerWrapper.framework` 和认知服务语音 SDK 的框架（可从[此处](https://aka.ms/csspeech/iosbinary)下载）复制到包含示例项目的目录。
1. 在*项目设置*中调整框架的路径。
   1. 在 "**常规**" 选项卡的 "**嵌入的二进制文件**标头" 下，将 SDK 库添加为框架：**添加嵌入的二进制文件** > **添加其他 ...** > 导航到所选的目录，并选择两个框架。
   1. 转到“生成设置”选项卡，激活“所有”设置。
1. 将目录 `$(SRCROOT)/..` 添加到“搜索路径”标头下的“框架搜索路径”。
