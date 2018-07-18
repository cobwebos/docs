---
title: 开始使用 Android 上 Java 中的 Microsoft 语音识别 API | Microsoft Docs
description: 使用 Microsoft 语音 API 开发将语音音频转换为文本的 Android 应用程序。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: a10f7be1c36fb431016a9867f606e26be858069e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365683"
---
# <a name="get-started-with-speech-recognition-in-java-on-android"></a>开始使用 Android 上 Java 中的语音识别

通过语音识别 API，可以开发使用基于云的语音服务将语音音频转换为文本的 Android 应用程序。 API 支持实时流式处理，因此应用程序可以在将音频发送到服务的同时以同步和异步方式接收部分识别结果。

本文将使用示例应用程序来演示如何使用适用于 Android 的语音客户端库在 Java 中为 Android 设备开发语音到文本应用程序。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

此示例由适用于 Windows 的 [Android Studio](http://developer.android.com/sdk/index.html) 通过 Java 开发。

### <a name="get-the-client-library-and-sample-application"></a>获取客户端库和示例应用程序

[适用于 Android 的语音客户端 SDK](https://github.com/microsoft/cognitive-speech-stt-android) 提供适用于 Android 的语音客户端库和示例。 可以在 samples/SpeechRecoExample 目录下找到可生成示例。 可以在 SpeechSDK/libs 的 armeabi 和 x86 文件夹下找到需要在自己的应用中使用的两个库。 libandroid_platform.so 文件的大小为 22 MB，但在部署时会缩减到 4 MB。

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>订阅语音 API，并获得免费试用的订阅密钥

语音 API 是认知服务（之前称为 Project Oxford）的一部分。 可以从[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页面获取免费试用的订阅密钥。 选择语音 API 后，选择“获取 API 密钥”以获取密钥。 它将返回主密钥和辅助密钥。 两个密钥都绑定到相同的配额，因此可以使用任一密钥。

如果想使用“意向识别”，还需要注册[语言理解智能服务 (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)。

> [!IMPORTANT]
>* 获取订阅密钥。 必须拥有[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)才能使用语音客户端库。
>
>* 使用订阅密钥。 借助提供的 Android 示例应用程序，使用订阅密钥更新 samples/SpeechRecoExample/res/values/strings.xml 文件。 有关详细信息，请参阅[生成并运行示例](#build-and-run-samples)。

## <a name="use-the-speech-client-library"></a>使用语音客户端库

若要使用应用程序中的客户端库，请按照[说明](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library)进行操作。

可以在[适用于 Android 的语音客户端 SDK](https://github.com/microsoft/cognitive-speech-stt-android) 的文档文件夹中找到适用于 Android 客户端库参考。

## <a name="build-and-run-samples"></a>生成并运行示例

要了解如何生成和运行示例，请参阅此 [README 页面](https://github.com/microsoft/cognitive-speech-stt-android#the-sample)。

## <a name="samples-explained"></a>介绍的示例

### <a name="create-recognition-clients"></a>创建识别客户端

以下示例中的代码演示如何基于用户方案创建识别客户端类：

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

客户端库为语音识别的典型方案提供预实现的识别客户端类：

* `DataRecognitionClient`：PCM 数据（例如，来自文件或音频源）的语音识别。 数据被分解到多个缓冲区后，再将每个缓冲区发送到语音服务。 未对缓冲区做出更改，所以用户可以按需应用自己的静音检测。 如果数据来自 WAV 文件，则可以直接将数据从文件发送到语音服务。 如果有原始数据（例如蓝牙音频），则首先应将格式标头发送到语音服务，然后再发送该数据。
* `MicrophoneRecognitionClient`：通过麦克风音频进行语音识别。 请确保麦克风已开启，且麦克风数据已发送到语音识别服务。 内置“静音检测器”在发送到识别服务前已应用到麦克风数据。
* `DataRecognitionClientWithIntent` 和 `MicrophoneRecognitionClientWithIntent`：除了识别文本，这些客户端返回有关说话人意向的结构化信息，应用程序可以用这些信息来推动进一步的操作。 要使用“意向”检测，需要首先使用 [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 定型模型。

### <a name="recognition-language"></a>识别语言

使用 `SpeechRecognitionServiceFactory` 创建客户端时，必须选择一种语言。 有关语音服务支持语言的完整列表，请参阅[支持的语言](../API-Reference-REST/supportedlanguages.md)。

### `SpeechRecognitionMode`

使用 `SpeechRecognitionServiceFactory` 创建客户端时，还需要指定 `SpeechRecognitionMode`：

* `ShortPhrase`：最长 15 秒的语音。 将数据发送到服务时，客户端接收到多个部分结果和一个具有多个 n-best 选择的最终结果。
* `LongDictation`：最长两分钟的陈述。 将数据发送到服务时，基于服务指示的语句停顿位置，客户端会接收到多个部分结果和多个最终结果。

### <a name="attach-event-handlers"></a>附加事件处理程序

可以将各种事件处理程序附加到创建的客户端：

* **部分结果事件**：每次语音服务预测你可能会说的内容时，甚至在你说完（如果使用 `MicrophoneRecognitionClient`）或完成发送数据（如果使用 `DataRecognitionClient`）之前，都会调用此事件。
* **错误事件**：服务检测到错误时调用。
* **意向事件**：在最终识别结果被解析为结构化 JSON 意向后，在“WithIntent”客户端（仅限 `ShortPhrase` 模式）进行调用。
* **结果事件**：
  * 在 `ShortPhrase` 模式下，在你说完后调用此事件并返回 n-best 结果。
  * 在 `LongDictation` 模式下，基于服务指示的语句停顿位置，多次调用事件处理程序。
  * 对于每一个 n-best 选择，将返回一个可信度值和一些不同形式的识别文本。 有关详细信息，请参阅[输出格式](../Concepts.md#output-format)。

## <a name="related-topics"></a>相关主题

* [适用于 Android 的客户端库参考](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [在 .NET 中适用于 Windows 的 C# 中开始使用 Microsoft 语音 API](GetStartedCSharpDesktop.md)
* [在 iOS 上的 Objective-C 中开始使用 Microsoft 语音 API](Get-Started-ObjectiveC-iOS.md)
* [在 JavaScript 中开始使用 Microsoft 语音 API](GetStartedJSWebsockets.md)
* [通过 REST 开始使用 Microsoft 语音 API](GetStartedREST.md)
