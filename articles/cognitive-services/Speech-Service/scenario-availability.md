---
title: 方案可用性-语音服务
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
ms.openlocfilehash: e8fc6e8c2f37dcd3edec24fb4d8ed81b32a84bd0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816594"
---
# <a name="scenario-availability"></a>方案可用性

语音 SDK 在各种编程语言和环境中提供多种方案。 并非所有方案都适用于所有编程语言或所有环境。 下面列出了每个方案的可用性。

- **语音识别（SR）、短语列表、意向、翻译和本地容器**
  - 存在箭头链接的所有编程语言/环境 <img src="media/index/link.jpg" height="15" width="15"></img> 在[此处](https://aka.ms/csspeech)。
- **文本到语音转换（TTS）**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java （Jre 和 Android）
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可以在其他所有情况下使用。
- **关键字发现（KWS）**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android （语音设备 SDK）
  - 关键字发现（KWS）功能可能适用于任何麦克风类型，但官方 KWS 支持目前仅限于在 Azure Kinect 深色硬件或语音设备 SDK 中找到的麦克风阵列
- **语音助手**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android （语音设备 SDK）
- **对话听录**
  - C++/Windows & Linux
  - C#（Framework & .NET Core）/Windows & UWP & Linux
  - Java/Windows & Linux & Android （语音设备 SDK）
- **呼叫中心脚本**
  - REST API，在任何情况下都可以使用
- **编解码器压缩的音频输入**
  - C++/Linux
  - C#/Linux
  - Java/Linux、Android 和 iOS
