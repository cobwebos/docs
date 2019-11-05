---
title: 利用语音 SDK-语音服务开发应用程序
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 创建应用。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491004"
---
# <a name="ship-an-application"></a>交付应用程序

分发 Azure 认知服务语音 SDK 时，阅读[语音 SDK 许可](https://aka.ms/csspeech/license201809)和[第三方软件通知](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)。 此外，请查看 [Microsoft 隐私声明](https://aka.ms/csspeech/privacy)。

不同平台存在不同的依赖项来执行应用程序。

## <a name="windows"></a>Windows

认知服务语音 SDK 在 Windows 10 和 Windows Server 2016 上进行测试。

认知服务语音 SDK 要求系统上安装有 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 可在此处下载最新版 `Microsoft Visual C++ Redistributable for Visual Studio 2019` 安装程序：

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

如果应用程序使用托管代码，则目标计算机上需要 `.NET Framework 4.6.1` 或更高版本。

对于麦克风输入，必须安装媒体基础库。 这些库包含在 Windows 10 和 Windows Server 2016 中。 只要未将麦克风用作音频输入设备，则可在没有这些库的情况下使用语音 SDK。

所需语音 SDK 文件可部署在与应用程序相同的目录中。 这样，应用程序便可直接访问库。 请确保选择与应用程序匹配的正确版本 (Win32/x64)。

| 名称 | 函数
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | 核心 SDK，对于本机和托管部署是必需的
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 对于托管部署是必需的

>[!NOTE]
> 从版本 1.3.0 开始，不再需要 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 文件（在以前的版本中提供）。 此功能现在集成到核心 SDK 中。

>[!NOTE]
> 对于 Windows 窗体应用 (.NET Framework) C# 项目，请确保项目的部署设置中包含这些库。 你可以在 `Properties -> Publish Section` 下查看此内容。 单击 `Application Files` 按钮并从向下滚动列表中查找相应的库。 请确保将值设置为 `Included`。 Visual Studio 将在发布/部署项目时包含该文件。

## <a name="linux"></a>Linux

语音 SDK 目前支持 Ubuntu 16.04、Ubuntu 18.04 和 Debian 9 发行版。
对于本机应用程序，需要交付语音 SDK 库 `libMicrosoft.CognitiveServices.Speech.core.so`。
请确保选择与应用程序匹配的版本（x86、x64）。 根据 Linux 版本，可能还需要包括以下依赖项：

* GNU C 库的共享库（包括 POSIX 线程编程库 `libpthreads`）
* OpenSSL 库（`libssl.so.1.0.0` 或 `libssl.so.1.0.2`）
* ALSA 应用程序的共享库 (`libasound.so.2`)

举例来说，应该已默认安装 GNU C 库。 可使用以下命令安装后三个依赖项：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

在 Debian 9 上安装以下包：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
