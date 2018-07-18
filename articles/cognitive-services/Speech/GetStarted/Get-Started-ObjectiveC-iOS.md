---
title: 在 iOS 上的 Objective-C 中开始使用 Microsoft 语音识别 API | Microsoft Docs
description: 使用 Microsoft 语音识别 API 开发用于将语音转换为文本的 iOS 应用程序。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: bbb8d3975cdab537135b97ca9bbf6e845aa3fa0e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365680"
---
# <a name="get-started-with-the-speech-recognition-api-in-objective-c-on-ios"></a>在 iOS 上的 Objective-C 中开始使用语音识别 API

通过语音识别 API，可以开发使用基于云的语音服务将语音转换为文本的 iOS 应用程序。 API 支持实时流式处理，因此应用程序可以在将音频发送到服务的同时以同步和异步方式接收部分识别结果。

本文使用一个示例应用程序演示如何开始使用语音识别 API 开发 iOS 应用程序的基础知识。 有关完整的 API 参考，请参阅[语音 SDK 客户端库参考](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

请确保已安装 Mac XCode IDE。

### <a name="get-the-client-library-and-examples"></a>获取客户端库和示例

可从[用于 iOS 的语音客户端 SDK](https://github.com/microsoft/cognitive-speech-stt-ios) 获取适用于 iOS 的语音客户端库和示例。

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>订阅语音识别 API 并获取免费试用的订阅密钥

语音 API 是认知服务（之前称为 Project Oxford）的一部分。 可从[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页面获取免费试用的订阅密钥。 选择语音 API 后，选择“获取 API 密钥”以获取密钥。 它将返回主密钥和辅助密钥。 两个密钥都绑定到相同的配额，因此可以使用任一密钥。

如果想使用“意向识别”，还需要注册[语言理解智能服务 (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)。

> [!IMPORTANT]
> * 获取订阅密钥。 必须拥有[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)才能使用语音客户端库。
>
> * 使用订阅密钥。 有了提供的 iOS 示例应用程序，需要使用订阅密钥更新文件 Samples/SpeechRecognitionServerExample/settings.plist。 有关详细信息，请参阅[生成和运行示例](#build-and-run-samples)。

## <a name="use-the-speech-client-library"></a>使用语音客户端库

要将客户端库添加到 XCode 项目，请按照这些[说明](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library)进行操作。

要查找适用于 iOS 的客户端库参考，请参阅此[网页](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)。

## <a name="build-and-run-samples"></a>生成并运行示例

有关如何生成和运行示例的信息，请参阅此[自述页面](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample)。

## <a name="samples-explained"></a>介绍的示例

### <a name="create-recognition-clients"></a>创建识别客户端

示例中的以下代码演示如何基于用户方案创建识别客户端类：

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

客户端库为语音识别的典型方案提供预实现的识别客户端类：

* `DataRecognitionClient`：通过 PCM 数据进行语音识别（例如，从文件或音频源）。 数据被分解到多个缓冲区后，再将每个缓冲区发送到语音服务。 未对缓冲区做出任何更改，因此，如果需要，用户可以应用其自己的静音检测。 如果数据来自 WAV 文件，则可以直接将数据从文件发送到服务器。 如果有原始数据（例如蓝牙音频），则首先应将格式标头发送到服务器，然后再发送该数据。
* `MicrophoneRecognitionClient`：对来自麦克风的音频进行语音识别。 请确保麦克风已开启，且麦克风数据已发送到语音识别服务。 内置“静音检测器”在发送到识别服务前已应用到麦克风数据。
* `DataRecognitionClientWithIntent` 和 `MicrophoneRecognitionClientWithIntent`：除了识别文本，这些客户端还会返回有关说话人意向的结构化信息，应用程序可以用它来促进更多操作。 要使用“意向”，需要首先使用 [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 定型模型。

### <a name="recognition-language"></a>识别语言

使用 `SpeechRecognitionServiceFactory` 创建客户端时，必须选择一种语言。 有关语音服务支持的完整语言列表，请参阅[支持的语言](../API-Reference-REST/supportedlanguages.md)。

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

使用 `SpeechRecognitionServiceFactory` 创建客户端时，还需要指定 `SpeechRecognitionMode`：

* `SpeechRecognitionMode_ShortPhrase`：最长 15 秒的语音。 当将数据发送到服务时，客户端接收多个部分结果和一个具有多个 n-best 选择的最终结果。
* `SpeechRecognitionMode_LongDictation`：长达两分钟的语音。 当将数据发送到服务时，基于服务器标识的语句停顿位置，客户端会接收到多个部分结果和多个最终结果。

### <a name="attach-event-handlers"></a>附加事件处理程序

可以将各种事件处理程序附加到创建的客户端：

* **部分结果事件**：每次语音服务预测你可能会说的内容时，甚至在你说完（如果使用 `MicrophoneRecognitionClient`）或完成发送数据（如果使用 `DataRecognitionClient`）之前，都会调用此事件。
* **错误事件**：服务检测到错误时调用。
* **意向事件**：在最终识别结果被解析为结构化 JSON 意向后，在“WithIntent”客户端（仅限 ShortPhrase 模式）进行调用。
* **结果事件**：
  * 在 `SpeechRecognitionMode_ShortPhrase` 模式下，在你说完后调用此事件并返回 n-best 结果。
  * 在 `SpeechRecognitionMode_LongDictation` 模式下，基于服务指示的语句停顿位置，多次调用事件处理程序。
  * 对于每一个 n-best 选择，都将返回一个可信度值和一些不同窗体的识别文本。 有关详细信息，请参阅[输出格式](../Concepts.md#output-format)。

## <a name="related-topics"></a>相关主题

* [适用于 iOS 的客户端库参考](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [在 Android 上的 Java 中开始使用 Microsoft 语音识别和/或意向](GetStartedJavaAndroid.md)
* [在 JavaScript 中开始使用 Microsoft 语音 API](GetStartedJSWebsockets.md)
* [通过 REST 开始使用 Microsoft 语音 API](GetStartedREST.md)
