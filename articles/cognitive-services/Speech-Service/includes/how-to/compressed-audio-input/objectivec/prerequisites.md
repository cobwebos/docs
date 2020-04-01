---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409680"
---
处理压缩音频是使用 [GStreamer](https://gstreamer.freedesktop.org) 实现的。 出于许可原因，GStreamer 二进制文件未编译并与语音 SDK 链接。 相反，需要构建包含这些功能的包装库，并使用 SDK 随应用一起提供。

若要构建该包装器库，请先下载并安装 [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)。 然后，下载[包装库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)的**Xcode**项目。

在**Xcode**中打开项目并为通用**iOS 设备**目标构建项目 - 它*不起作用*为特定目标构建该项目。

构建步骤会生成一个动态框架捆绑包（其中的动态库适用于所有必需的体系结构），其名称为 `GStreamerWrapper.framework`。

此框架必须包含在所有通过语音服务 SDK 使用压缩音频流的应用中。

在**Xcode**项目中应用以下设置来实现此目的：

1. 将`GStreamerWrapper.framework`您刚刚构建的和认知服务语音 SDK 的框架（可以[从这里](https://aka.ms/csspeech/iosbinary)下载）复制到包含示例项目的目录。
1. 在“项目设置”中调整框架的路径。**
   1. 在 **"嵌入式二进制文件"** 标题下的 **"常规**"选项卡中，将 SDK 库添加为框架：**添加嵌入式二进制文件** > **添加其他...** >导航到您选择的目录并选择两个框架。
   1. 转到“生成设置”**** 选项卡，激活“所有”设置****。
1. 将目录 `$(SRCROOT)/..` 添加到“搜索路径”标头下的“框架搜索路径”。__****
