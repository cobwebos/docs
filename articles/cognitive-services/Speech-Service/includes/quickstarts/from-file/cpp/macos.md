---
title: 快速入门：从音频文件中识别语音，C++ (macOS) - 语音服务
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: erhopf
ms.openlocfilehash: 622087a914b77712df850d4200ac381439f3bd85
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446056"
---
## <a name="prerequisites"></a>必备条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建 Azure 语音资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=macos)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 创建一个名为 `helloworld.cpp` 的 C++ 源文件，并将以下代码粘贴到其中。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. 在此新文件中，将字符串 `YourSubscriptionKey` 替换为你的语音服务订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](https://aka.ms/speech/sdkregion)中的“区域标识符”（例如，对于免费试用版订阅，为 `westus`）。 

1. 将 `whatstheweatherlike.wav` 字符串替换为你自己的文件名。

> [!NOTE]
> 语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="build-the-app"></a>生成应用

> [!NOTE]
> 请确保将以下命令输入在单个命令行上。  执行该操作的最简单方法是使用每个命令旁边的“复制按钮”来复制命令，然后将其粘贴到 shell 提示符下。 

* 运行以下命令以生成应用程序。

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>运行应用

1. 将加载程序的库路径配置为指向语音 SDK 库。

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. 运行应用程序。

   ```sh
   ./helloworld
   ```

1. 你的音频文件将传输到语音服务，文件中的第一个言语将转录为文本，该文本将显示在同一窗口中。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
