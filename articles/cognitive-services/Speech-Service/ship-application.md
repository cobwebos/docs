---
title: 利用语音 SDK-语音服务开发应用程序
titleSuffix: Azure Cognitive Services
description: 了解如何在支持的平台上部署使用 Speech SDK 的应用程序。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 4f75adba27c8173f918fa1afbd44f307d50eb995
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902025"
---
# <a name="ship-an-application"></a>交付应用程序

请在分发 Azure 认知服务语音 SDK 时，查看[语音 sdk 许可证](https://aka.ms/csspeech/license201809)以及[第三方软件通知](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)。 另外，请查看[Microsoft 隐私声明](https://aka.ms/csspeech/privacy)。

对于执行应用程序，存在不同的依赖项，具体取决于平台。

## <a name="windows"></a>Windows

认知服务语音 SDK 在 Windows 10 和 Windows Server 2016 上经过测试。

认知服务语音 SDK 需要在系统上安装[适用于 Visual Studio 2019 的 Microsoft visual C++可再发行组件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 可在此处下载最新版本的安装程序 `Microsoft Visual C++ Redistributable for Visual Studio 2019`：

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

如果你的应用程序使用托管代码，则目标计算机上需要 `.NET Framework 4.6.1` 或更高版本。

对于麦克风输入，必须安装媒体基础库。 这些库属于 Windows 10 和 Windows Server 2016。 只要没有麦克风用作音频输入设备，就可以使用没有这些库的语音 SDK。

所需的语音 SDK 文件可以部署在与应用程序相同的目录中。 这样，应用程序就可以直接访问这些库。 请确保选择与您的应用程序匹配的正确版本（Win32/x64）。

| 姓名 | 函数 |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | 核心 SDK，本地和托管部署所必需 |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 托管部署所必需                      |

> [!NOTE]
> 从 release 1.3.0 开始，不再需要文件 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` （在以前的版本中随附）。 此功能现已在核心 SDK 中集成。

> [!NOTE]
> 对于 Windows 窗体应用（.NET Framework） C#项目，请确保项目的部署设置中包含这些库。 可以在 `Properties -> Publish Section`下检查此情况。 单击 "`Application Files`" 按钮，然后从滚动列表中查找相应的库。 请确保将值设置为 `Included`。 发布/部署项目时，Visual Studio 将包括该文件。

## <a name="linux"></a>Linux

语音 SDK 目前支持 Ubuntu 16.04、Ubuntu 18.04 和 Debian 9 分发版。
对于本机应用程序，你需要提供语音 SDK 库，`libMicrosoft.CognitiveServices.Speech.core.so`。
请确保选择与您的应用程序匹配的版本（x86、x64）。 根据 Linux 版本，你可能还需要包含以下依赖项：

- GNU C 库的共享库（包括 POSIX 线程编程库，`libpthreads`）
- OpenSSL 库（`libssl.so.1.0.0` 或 `libssl.so.1.0.2`）
- 用于 ALSA 应用程序的共享库（`libasound.so.2`）

在 Ubuntu 上，默认情况下，GNU C 库应已安装。 可以使用以下命令安装最后三个：

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

- [获取你的语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [了解如何识别中的语音C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
