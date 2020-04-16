---
title: 方案可用性 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音 SDK 在各种编程语言和环境中提供多种方案。 并非所有方案都在所有编程语言或所有环境中可用。 下面列出了每个方案的可用性。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400149"
---
# <a name="scenario-availability"></a>方案可用性

语音 SDK 在各种编程语言和环境中提供多种方案。 并非所有方案都在所有编程语言或所有环境中可用。 下面列出了每个方案的可用性。

- **语音识别 （SR）、短语列表、意图、翻译和本地容器**
  - C++/视窗&linux&macOS
  - C# （框架& .NET 核心）/Windows & UWP &统一& Xamarin & Linux & macOS
  - Java（Jre 和 Android）
  - JavaScript（布罗伯和节点JS）
  - Python
  - Swift
  - Objective-C  
- **文本到语音 （TTS）**
  - C++/Windows 和 Linux
  - C#/Windows 和 UWP 和 Unity
  - Java（Jre 和 Android）
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可以在所有其他情况下使用。
- **关键词识别 （KWS）**
  - C++/Windows 和 Linux
  - C#/Windows 和 Linux
  - Python/Windows 和 Linux
  - Java/Windows 和 Linux 和 Android（语音设备 SDK）
  - 关键字发现 （KWS） 功能可能适用于任何麦克风类型，但官方 KWS 支持目前仅限于 Azure Kinect DK 硬件或语音设备 SDK 中的麦克风阵列
- **语音助手**
  - C++/视窗&linux&macOS
  - C#/Windows
  - Java/Windows&Linux& macOS & Android（语音设备 SDK）
- **对话听录**
  - C++/Windows 和 Linux
  - C#（Framework 和 .NET Core）/Windows 和 UWP 和 Linux
  - Java/Windows 和 Linux 和 Android（语音设备 SDK）
- **多设备对话**
  - C++/窗户
  - C# （框架& .NET 核心）/窗口
- **呼叫中心转录**
  - REST API，可以在任何情况下使用
- **编解码器压缩的音频输入**
  - C++/Linux
  - C#/Linux
  - Java/Linux、Android 和 iOS
