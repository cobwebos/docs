---
title: 方案可用性-语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务区域的参考。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 94fd415909e86a43916ee2f510732a6a6d9c5ed3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552952"
---
# <a name="scenario-availability"></a>方案可用性

语音服务 SDK 在各种编程语言和环境中提供多种方案。  并非所有方案目前都适用于所有编程语言或所有环境。  下面列出了每个方案的可用性。

- **语音识别 (SR)、短语列表、意向、翻译和本地容器**
  - 存在箭头链接的所有编程语言/环境 <img src="media/index/link.jpg" height="15" width="15"></img> 在[此处](https://aka.ms/csspeech)。
- **文本到语音转换 (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - TTS REST API 可以在其他所有情况下使用。
- **唤醒词 (关键字 Spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (语音设备 SDK)
  - 唤醒 Word (关键字 Spotter/KWS) 功能可能适用于任何麦克风类型, 但官方 KWS 支持目前仅限于在 Azure Kinect 深色硬件或语音设备 SDK 中找到的麦克风阵列
- **语音-首次虚拟助手**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (语音设备 SDK)
- **对话听录**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (语音设备 SDK)
- **呼叫中心脚本**
  - REST API, 在任何情况下都可以使用
- **编解码器压缩的音频输入**
  - C++/Linux
  - C#/Linux
  - Java/Linux & Android
