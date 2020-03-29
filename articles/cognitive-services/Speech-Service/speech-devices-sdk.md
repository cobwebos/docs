---
title: 语音设备 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 入门。 “语音服务”适用于多种设备和音频源。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370638"
---
# <a name="what-is-the-speech-devices-sdk"></a>什么是语音设备 SDK？

[语音服务](overview.md)适用于各种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。

语音设备 SDK 有助于：

- 快速测试新的语音方案。
- 更轻松地将基于云的语音服务集成到设备中。
- 为客户创建出色的用户体验。

语音设备 SDK 使用[语音 SDK](speech-sdk.md)。 使用我们先进的音频处理算法与设备的麦克风阵列发送音频到[语音服务](overview.md)。 它通过噪声抑制、回声消除、波束成形和脱混，提供精确的远场[语音识别](speech-to-text.md)。

您还可以使用语音设备 SDK 构建具有自己[自定义关键字](speech-devices-sdk-create-kws.md)的环境设备。 自定义关键字提供提示，启动您的品牌独有的用户交互。

语音设备 SDK 支持各种支持语音的方案，例如[语音助理](https://aka.ms/bots/speech/va)、驱动器式排序系统、[对话转录](conversation-transcription-service.md)和智能扬声器。 可以使用文本回复用户、用默认或[自定义语音](how-to-customize-voice-font.md)回答用户、提供搜索结果以及[翻译](speech-translation.md)为其他语言等。 我们期待看到你的成果！

## <a name="get-the-speech-devices-sdk"></a>获取语音设备 SDK

### <a name="android"></a>Android

对于 Android，设备下载最新版本的[Android 语音设备 SDK](https://aka.ms/sdsdk-download-android)。

### <a name="windows"></a>Windows

对于 Windows，示例应用程序作为跨平台 Java 应用程序提供。 下载最新版本的 [JRE 语音设备 SDK](https://aka.ms/sdsdk-download-JRE)。
该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Windows 上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

### <a name="linux"></a>Linux

对于 Linux，示例应用程序作为跨平台 Java 应用程序提供。 下载最新版本的 [JRE 语音设备 SDK](https://aka.ms/sdsdk-download-JRE)。
该应用程序使用语音 SDK 包和 64 位 Linux 上的 Eclipse Java IDE （v4） 构建（Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

提供了额外的二进制文件来支持即将推出的设备[，Roobo v2 DDK，Urbetter](https://aka.ms/sdsdk-download-roobov2) [DDK](https://aka.ms/sdsdk-download-urbetter)和[GGEC扬声器](https://aka.ms/sdsdk-download-speaker)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择语音设备](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [免费获取语音服务订阅密钥](get-started.md)
