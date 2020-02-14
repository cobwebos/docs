---
title: 关于语音设备 SDK-语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 入门。 语音服务适用于各种设备和音频源。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: erhopf
ms.openlocfilehash: 854e7beb2afd8ae838455f77ff448f13d8b3fbea
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188979"
---
# <a name="about-the-speech-devices-sdk"></a>关于语音设备 SDK

[语音服务](overview.md)适用于各种设备和音频源。 现在，可以通过匹配的硬件和软件进一步利用语音应用程序。 语音设备 SDK 是与特制的麦克风阵列开发工具包配对的一个预优化库。

语音设备 SDK 有助于：

- 快速测试新的语音方案。
- 更轻松地将基于云的语音服务集成到设备中。
- 为客户创建出色的用户体验。

语音设备 SDK 使用[语音 SDK](speech-sdk.md)。 将高级音频处理算法与设备的麦克风阵列一起使用，将音频发送到[语音服务](overview.md)。 它通过干扰干扰、回声取消、beamforming 和 dereverberation 提供准确的现场[语音识别](speech-to-text.md)。

你还可以使用语音设备 SDK 来构建具有你自己的[自定义关键字](speech-devices-sdk-create-kws.md)的环境设备。 自定义关键字提供了一个提示，用于启动用户交互，这对你的品牌是唯一的。

语音设备 SDK 启用各种语音支持方案，如[语音助手](https://aka.ms/bots/speech/va)、驱动器到订购系统、[对话](conversation-transcription-service.md)脚本和智能扬声器。 可以使用文本回复用户、用默认或[自定义语音](how-to-customize-voice-font.md)回答用户、提供搜索结果以及[翻译](speech-translation.md)为其他语言等。 我们期待看到你的成果！

## <a name="get-the-speech-devices-sdk"></a>获取语音设备 SDK

### <a name="android"></a>Android

对于 Android，设备会下载最新版本的[Android 语音设备 SDK](https://aka.ms/sdsdk-download-android)。

### <a name="windows"></a>Windows

对于 Windows，示例应用程序是以跨平台的 Java 应用程序的形式提供的。 下载最新版本的[JRE 语音设备 SDK](https://aka.ms/sdsdk-download-JRE)。
该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Windows 上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

### <a name="linux"></a>Linux

对于 Linux，示例应用程序是以跨平台 Java 应用程序的形式提供的。 下载最新版本的[JRE 语音设备 SDK](https://aka.ms/sdsdk-download-JRE)。
该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Linux（Ubuntu 16.04、Ubuntu 18.04、Debian 9）上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

提供额外的二进制文件以支持即将推出的设备、 [Roobo V2 ddk](https://aka.ms/sdsdk-download-roobov2)和[Urbetter 的 ddk](https://aka.ms/sdsdk-download-urbetter)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择语音设备](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [免费获取语音服务订阅密钥](get-started.md)
