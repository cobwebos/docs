---
title: 快速入门：用于 C++ (macOS) 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用本指南，通过语音服务 SDK 设置用于 C++ on macOS 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504465"
---
本指南介绍如何安装用于 macOS 10.13 及更高版本上的 C++ 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>系统要求

macOS 10.13 及更高版本

## <a name="install-speech-sdk"></a>安装语音 SDK

1. 选择应将语音 SDK 文件提取到的目录，然后将 `SPEECHSDK_ROOT` 环境变量设置为指向该目录。 使用此变量，在将来的命令中可以轻松引用目录。 例如，如果要使用主目录中的 `speechsdk` 目录，请使用如下所示的命令：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 如果该目录尚不存在，请创建该目录。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下载并提取包含语音 SDK 框架的 `.zip` 存档：

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 验证所提取的程序包的顶级目录的内容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   目录列表应包含第三方声明和许可证文件，以及 `MicrosoftCognitiveServicesSpeech.framework` 目录。

现在可以继续完成下面的[后续步骤](#next-steps)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
