---
title: 快速入门：从音频文件中识别语音，C# (.NET) - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: fdb747212914769b8551d9cd12f1fbc8a01245dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506345"
---
## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [创建一个空示例项目](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中打开项目

第一步是确保已在 Visual Studio 中打开项目。

1. 启动 Visual Studio 2019。
2. 加载项目并打开 `Program.cs`。

## <a name="start-with-some-boilerplate-code"></a>从一些样板代码开始

让我们添加一些代码作为项目的主干。 请注意，已创建一个名为 `RecognizeSpeechAsync()` 的异步方法。

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task RecognizeSpeechAsync()
        {
        }

        static void Main()
        {
            RecognizeSpeechAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>创建语音配置

在初始化 `SpeechRecognizer` 对象之前，需要创建一个使用订阅密钥和订阅区域的配置。 将此代码插入 `RecognizeSpeechAsync()` 方法。

> [!NOTE]
> 此示例使用 `FromSubscription()` 方法来生成 `SpeechConfig`。 有关可用方法的完整列表，请参阅 [SpeechConfig 类](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。

````C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>创建应用程序配置

现在，需要创建指向音频文件的 ````AudioConfig```` 对象。 此对象是在 using 语句中创建的，以确保正确释放非托管资源。 将此代码插入语音配置下的 `RecognizeSpeechAsync()` 方法。

````C#
using (var audioInput = AudioConfig.FromWavFileInput(@"whatstheweatherlike.wav"))
{
}
````

## <a name="initialize-a-speechrecognizer"></a>初始化 SpeechRecognizer

现在，使用之前创建的 `SpeechConfig` 和 `AudioConfig` 对象创建 `SpeechRecognizer` 对象。 此对象也是在 using 语句中创建的，以确保正确释放非托管资源。 将此代码插入 `RecognizeSpeechAsync()` 方法中，用于包装 ````AudioConfig```` 对象的 using 语句中。

````C#
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
````

## <a name="recognize-a-phrase"></a>识别短语

在 `SpeechRecognizer` 对象中，我们将调用 `RecognizeOnceAsync()` 方法。 此方法是告知语音服务你要发送单个需识别的短语，在确定该短语后会停止识别语音。

在 using 语句中，添加以下代码：
````C#
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
````

## <a name="display-the-recognition-results-or-errors"></a>显示识别结果（或错误）

如果语音服务返回了识别结果，则需执行一些操作。 我们会简单地将结果输出到控制台。

在 using 语句中 `RecognizeOnceAsync()` 的下面，添加以下代码：
````C#
if (result.Reason == ResultReason.RecognizedSpeech)
{
    Console.WriteLine($"We recognized: {result.Text}");
}
else if (result.Reason == ResultReason.NoMatch)
{
    Console.WriteLine($"NOMATCH: Speech could not be recognized.");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = CancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>检查代码

此时，代码应如下所示：

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput(@"whatstheweatherlike.wav"))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    Console.WriteLine("Recognizing first result...");
                    var result = await recognizer.RecognizeOnceAsync();

                    if (result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"We recognized: {result.Text}");
                    }
                    else if (result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            RecognizeSpeechAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>生成并运行应用

现在，已准备好使用语音服务构建应用并测我们的语音识别。

1. “编译代码”- 在 Visual Studio 菜单栏中，选择“生成” > “生成解决方案”    。
2. **启动应用** - 在菜单栏中，选择“调试” > “开始调试”，或按 F5    。
3. **开始识别** - 音频文件将发送到语音服务，听录为文本，并在控制台中呈现。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
