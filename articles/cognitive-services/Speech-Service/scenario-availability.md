---
title: 方案可用性 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务区域的参考。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6ec31df7cef8391728eae7845f64f55bb1c6466a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491345"
---
# <a name="scenario-availability"></a>方案可用性

语音服务 SDK 在各种编程语言和环境中提供多种方案。  目前，并非所有方案都在所有编程语言或所有环境中可用。  下面列出了每个方案的可用性。

- **语音识别（SR）、短语列表、意向、翻译和本地容器**
  - 快速入门表中有箭头链接的所有编程语言/环境 <img src="media/index/link.jpg" height="15" width="15"></img> 请参见[此处](https://aka.ms/csspeech)。
- **文本转语音 (TTS)**
  - C++/Windows 和 Linux
  - C#/Windows 和 UWP 和 Unity
  - Java （Jre 和 Android）
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可以在所有其他情况下使用。
- **关键字发现（KWS）**
  - C++/Windows 和 Linux
  - C#/Windows 和 Linux
  - Python/Windows 和 Linux
  - Java/Windows 和 Linux 和 Android（语音设备 SDK）
  - 关键字发现（KWS）功能可能适用于任何麦克风类型，但官方 KWS 支持目前仅限于在 Azure Kinect 深色硬件或语音设备 SDK 中找到的麦克风阵列
- **语音助手**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android （语音设备 SDK）
- **对话听录**
  - C++/Windows 和 Linux
  - C#（Framework 和 .NET Core）/Windows 和 UWP 和 Linux
  - Java/Windows 和 Linux 和 Android（语音设备 SDK）
- **呼叫中心听录**
  - REST API，可以在任何情况下使用
- **编解码器压缩的音频输入**
  - C++/Linux
  - C#/Linux
  - Java/Linux、Android 和 iOS
