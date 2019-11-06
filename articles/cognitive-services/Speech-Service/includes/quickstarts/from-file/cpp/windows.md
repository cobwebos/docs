---
title: 快速入门：从音频文件中识别语音，C++ (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Windows 桌面上使用语音 SDK 通过 C++ 识别语音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 09e0b6fbfcb8541a0756a855d4548e456a7b5c90
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506377"
---
## <a name="prerequisites"></a>先决条件

入门之前，准备工作有：

> [!div class="checklist"]
> * [创建 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>添加示例代码

1. 打开源文件 **helloworld.cpp**。

1. 将所有代码替换为以下片段：

   ````C++

    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech recognizer using a WAV file. The default language is "en-us".
    // Replace with your own audio file name.
    auto audioInput = AudioConfig::FromWavFileInput("whatstheweatherlike.wav");
    auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
    cout << "Recognizing first result...\n";

    // Starts speech recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    if (result->Reason == ResultReason::RecognizedSpeech)
    {
        cout << "RECOGNIZED: Text=" << result->Text << std::endl;
    }
    else if (result->Reason == ResultReason::NoMatch)
    {
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
    }
    else if (result->Reason == ResultReason::Canceled)
    {
        auto cancellation = CancellationDetails::FromResult(result);
        cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

        if (cancellation->Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
            cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
            cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }

   ````

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)（例如，免费试用版订阅的 `westus`）。

1. 将 `whatstheweatherlike.wav` 字符串替换为你自己的文件名。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动 helloworld  应用程序。

1. 你的音频文件将传输到语音服务，文件中的第一个言语将转录为文本，该文本将显示在同一窗口中。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]