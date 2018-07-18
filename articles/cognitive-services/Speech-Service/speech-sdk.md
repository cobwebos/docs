---
title: 关于认知服务语音 SDK | Microsoft Docs
description: 可用于语音服务的 SDK 的概述。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366816"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>关于认知服务语音 SDK

使用认知服务语音软件开发工具包 (SDK)，应用程序可以直接访问语音服务的功能，这使得软件开发工作更为容易。 当前，该 SDK 提供了对**语音到文本**、**语音翻译**和**意向识别**功能的访问。

下表列出了当前支持的编程语言和操作系统。

|支持的操作系统|编程语言|
|-|-|
|Windows|C/C++、C#|
|Linux|C/C++|
|设备|Java\*|

\* *Java SDK 是[语音设备 SDK](speech-devices-sdk.md) 的一部分。*

## <a name="get-the-windows-sdk"></a>获取 Windows SDK

语音 SDK 的 Windows 版本包括 32 位和 64 位 C/C++ 客户端库以及与 C# 一起使用的托管 (.NET) 库。 可以使用 NuGet 在 Visual Studio 中安装该 SDK；只需要搜索 `Microsoft.CognitiveServices.Speech` 即可。

## <a name="get-the-linux-sdk"></a>获取 Linux SDK

通过运行以下 shell 命令确保你具有必需的编译器和库：

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> 这些说明假设你在个人电脑（x86 或 x64）上运行 Ubuntu 16.04。 在不同的 Ubuntu 版本或不同的 Linux 发行版上，请根据你的环境调整这些步骤。

然后，[下载 SDK](https://aka.ms/csspeech/linuxbinary) 并将文件解压缩到你选择的一个目录中。 下表显示了 SDK 文件夹结构。

|路径|说明|
|-|-|
|`license.md`|许可证|
|`third-party-notices.md`|第三方声明|
|`include`|用于 C 和 C++ 的头文件|
|`lib/x64`|用于与应用程序链接的本机 x64 库|
|`lib/x86`|用于与应用程序链接的本机 x86 库|

若要创建应用程序，请将必需的二进制文件和（库）复制到开发环境中，并根据需要将它们包括到生成流程中。

## <a name="get-the-java-sdk"></a>获取 Java SDK

Java SDK 是[语音设备 SDK](speech-devices-sdk.md) 的一部分。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-windows.md)
