---
title: 快速入门：将语音转换为文本，C# (.NET Framework Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: e0983408859ff5e957cf0b2ec8d98b97a227c311
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445610"
---
## <a name="prerequisites"></a>必备条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>添加示例代码

1. 打开 **Program.cs** 并将其中的所有代码替换为以下内容。

   ```Csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Translation;

   namespace helloworld
   {
       class Program
       {
           public static async Task TranslateSpeechToText()
           {
               // Creates an instance of a speech translation config with specified subscription key and service region.
               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

               // Sets source and target languages.
               // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
               string fromLanguage = "en-US";
               string toLanguage = "de";
               config.SpeechRecognitionLanguage = fromLanguage;
               config.AddTargetLanguage(toLanguage);

               // Creates a translation recognizer using the default microphone audio input device.
               using (var recognizer = new TranslationRecognizer(config))
               {
                   // Starts translation, and returns after a single utterance is recognized. The end of a
                   // single utterance is determined by listening for silence at the end or until a maximum of 15
                   // seconds of audio is processed. The task returns the recognized text as well as the translation.
                   // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
                   // shot recognition like command or query.
                   // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
                   Console.WriteLine("Say something...");
                   var result = await recognizer.RecognizeOnceAsync();

                   // Checks result.
                   if (result.Reason == ResultReason.TranslatedSpeech)
                   {
                       Console.WriteLine($"RECOGNIZED '{fromLanguage}': {result.Text}");
                       Console.WriteLine($"TRANSLATED into '{toLanguage}': {result.Translations[toLanguage]}");
                   }
                   else if (result.Reason == ResultReason.RecognizedSpeech)
                   {
                       Console.WriteLine($"RECOGNIZED '{fromLanguage}': {result.Text} (text could not be translated)");
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

           static void Main(string[] args)
           {
               TranslateSpeechToText().Wait();
           }
       }
   }
   ```

1. 在同一文件中，将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)（例如，免费试用版订阅的 `westus`）。

1. 在菜单栏中，选择“文件”   > “全部保存”  。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动 helloworld  应用程序。

1. 说一个英语短语或句子。 应用程序将语音传输到语音服务，该服务会将语音翻译并转录为文本（在本例中为德语）。 然后，语音服务会将该文本发送回应用程序以供显示。

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
