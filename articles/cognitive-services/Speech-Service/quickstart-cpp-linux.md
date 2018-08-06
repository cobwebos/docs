---
title: 快速入门：使用认知服务语音 SDK 在 Linux 上的 C++ 中识别语音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用认知服务语音 SDK 在 Linux 上的 C++ 中识别语音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 8f794f1d29b5fde9acd43fa4a018ccb39a8ccd25
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324772"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Linux 上的 C++ 中识别语音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何使用认知服务语音 SDK 在 Linux (Ubuntu 16.04) 上创建 C++ 控制台应用程序，进而将语音转为文本。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 配有工作麦克风的 Ubuntu 16.04 电脑。
* 要安装生成和运行此示例所需的包，请运行以下命令：

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>获取语音 SDK

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

认知服务语音 SDK 的当前版本是 `0.5.0`。

适用于 Linux 的认知服务语音 SDK 可用于构建 64 位和 32 位应用程序。
所需的文件可以作为 tar 文件从 https://aka.ms/csspeech/linuxbinary 下载。
下载并安装 SDK，如下所示：

1. 选择要放置语音 SDK 二进制文件和头文件的目录（绝对路径）。
   例如，选择主目录下的路径 `speechsdk`：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 如果该目录尚不存在，则创建该目录：

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

   它应当会显示第三方通知和许可证文件，以及用于存放头文件的 `include` 目录和用于存放二进制文件的 `lib` 目录。

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>添加示例代码

1. 将以下代码添加到名为 `helloworld.cpp` 的文件中：

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. 将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

## <a name="building"></a>生成

> [!NOTE]
> 确保将下面的生成命令复制并粘贴为“单个行”。

* 在 x64 计算机上，运行下列命令以生成应用程序：

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* 在 x86 计算机上，运行下列命令以生成应用程序：

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>运行示例

1. 将加载程序的库路径配置为指向语音 SDK 库。

   * 在 x64 计算机上，请运行：

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * 在 x86 计算机上，请运行：

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. 如下运行应用程序：

   ```sh
   ./helloworld
   ```

1. 此时会看到与下面类似的输出：

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/cpp-linux` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

* [获取我们的示例](speech-sdk.md#get-the-samples)
