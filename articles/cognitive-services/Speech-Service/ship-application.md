---
title: Azure 认知服务、认知服务语音 SDK API 文档 - 教程、API 参考 | Microsoft Docs
description: 了解如何使用认知服务语音 SDK 创建和开发应用
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: d410dda09fdd30181b633c454b1d44610b10c472
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "35366987"
---
# <a name="shipping-an-application"></a>交付应用程序

分发认知服务语音 SDK 时，阅读[语音 SDK 许可](license.md)和[第三方软件通知](third-party-notices.md)。 此外，请查看 [Microsoft 隐私声明](https://aka.ms/csspeech/privacy)。

不同平台存在不同的依赖项来执行应用程序。

## <a name="windows"></a>Windows

认知服务语音 SDK 在 Windows 10 和 Windows Server 2016 上进行测试。

认知服务语音 SDK 要求系统上具有 [Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)。 可在此处下载最新版 `Microsoft Visual C++ Redistributable for Visual Studio 2017` 安装程序：

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

如果应用程序使用托管代码，则目标计算机上需要 `.Net Framework 4.6.1` 或更高版本。

对于麦克风输入，需要安装媒体基础库。 这些库包含在 Windows 10 和 Windows Server 2016 中。 只要未将麦克风用作音频输入设备，则可在没有这些库的情况下使用语音 SDK。

所需语音 SDK 文件可部署在与应用程序相同的目录中。 这样，应用程序便可直接访问库。 请确保选择与应用程序相匹配的正确版本 (Win32/x64)。

| 名称 | 函数
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | 核心 SDK，需用于本机和托管部署
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | 需用于托管部署
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 需用于托管部署

## <a name="linux"></a>Linux

对于本机应用程序，需要交付语音 SDK 库 `libMicrosoft.CognitiveServices.Speech.core.so`。
请确保选择与应用程序相匹配的版本（x86、x64）。 根据 Linux 版本，可能还需要包括以下依赖项：

* GNU C 库的共享库（包括 POSIX 线程编程库 `libpthreads`）
* OpenSSL 库 (`libssl.so.1.0.0`)
* cURL 库 (`libcurl.so.4`)
* ALSA 应用程序的共享库 (`libasound.so.2`)

举例来说，Ubuntu 16.04 上应该已默认安装 GNU C 库。 可使用以下命令安装后三个依赖项：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-windows.md)
