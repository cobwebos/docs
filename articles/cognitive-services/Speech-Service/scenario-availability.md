---
title: 方案可用性 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音 SDK 在各种编程语言和环境中提供多种方案。 并非所有方案都适用于所有编程语言或所有环境。 下面列出了每个方案的可用性。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 02632e5bc84c7406ce3a42eb4be71cf6aaf75c15
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075760"
---
# <a name="scenario-availability"></a>方案可用性

语音 SDK 在各种编程语言和环境中提供多种方案。 并非所有方案都适用于所有编程语言或所有环境。 下面列出了每个方案的可用性。

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
