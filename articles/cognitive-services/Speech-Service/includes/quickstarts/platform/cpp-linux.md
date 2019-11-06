---
title: 快速入门：用于 C++ (Linux) 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 设置用于 C++ on Linux 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 96e4c12d7b0aaceea3d652a81f2df8721c43954c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504545"
---
本指南介绍如何安装用于 Linux 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系统要求

Linux（Ubuntu 16.04、Ubuntu 18.04、Debian 9）

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* 支持的 Linux 平台会要求安装某些库（要求安装 `libssl` 以获取安全套接字层支持；要求安装 `libasound2` 以获取声音支持）。 请参阅下面的发行版，了解安装这些库的正确版本所需的命令。

   * 在 Ubuntu 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * 在 Debian 9 上：

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

## <a name="install-speech-sdk"></a>安装语音 SDK

适用于 Linux 的语音 SDK 可用于构建 64 位和 32 位应用程序。 可以从 https://aka.ms/csspeech/linuxbinary 以 tar 文件格式下载必需的库和头文件。

下载并安装 SDK，如下所示：

1. 选择应将语音 SDK 文件提取到的目录，然后将 `SPEECHSDK_ROOT` 环境变量设置为指向该目录。 使用此变量，在将来的命令中可以轻松引用目录。 例如，如果要使用主目录中的 `speechsdk` 目录，请使用如下所示的命令：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 如果该目录尚不存在，请创建该目录。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下载并提取包含语音 SDK 二进制文件的 `.tar.gz` 存档：

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 验证所提取的程序包的顶级目录的内容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   目录列表应当包含第三方通告和许可证文件，以及一个包含头文件 (`.h`) 的 `include` 目录和一个包含库的 `lib` 目录。

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

现在可以继续完成下面的[后续步骤](#next-steps)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
