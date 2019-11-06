---
title: 快速入门：从麦克风中识别语音，C++ (Linux) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Linux 上使用语音 SDK 通过 C++ 识别语音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 5236536abbe8ad7a415f0ea94036edf49b892052
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505633"
---
## <a name="prerequisites"></a>先决条件

入门之前，准备工作有：

> [!div class="checklist"]
> * [创建 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=linux)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=linux)

## <a name="add-sample-code"></a>添加示例代码

1. 创建一个名为 `helloworld.cpp` 的 C++ 源文件，并将以下代码粘贴到其中。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

1. 在此新文件中，将字符串 `YourSubscriptionKey` 替换为你的语音服务订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)（例如，免费试用版订阅的 `westus`）。

## <a name="build-the-app"></a>生成应用

> [!NOTE]
> 请确保将以下命令输入在单个命令行上。  执行该操作的最简单方法是使用每个命令旁边的“复制按钮”来复制命令，然后将其粘贴到 shell 提示符下。 

* 在 **x64**（64 位）系统上，运行以下命令来生成应用程序。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* 在 **x86**（32 位）系统上，运行以下命令来生成应用程序。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* 在 **ARM64**（64 位）系统上，运行以下命令生成应用程序。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>运行应用

1. 将加载程序的库路径配置为指向语音 SDK 库。

   * 在 **x64**（64 位）系统上，输入以下命令。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * 在 **x86**（32 位）系统上，输入以下命令。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * 在 **ARM64**（64 位）系统上，输入以下命令。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. 运行应用程序。

   ```sh
   ./helloworld
   ```

1. 在控制台窗口中，会出现一个提示，请求你说点什么。 说一个英语短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在同一窗口中。

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
