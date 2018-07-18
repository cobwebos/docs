---
title: 通过使用 C# 服务库开始使用 Microsoft 语音识别 API | Microsoft Docs
description: 使用 Microsoft 语音识别服务库将口语转换为文本。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/17/2017
ms.author: zhouwang
ms.openlocfilehash: 0320f41658a7ac4d6bf9e88ed998c853b665d485
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365693"
---
# <a name="get-started-with-the-speech-recognition-service-library-in-c35-for-net-windows"></a>开始使用 C# for .NET Windows 中的语音识别服务库

服务库适用于拥有自己的云服务并希望通过其服务调用语音服务的开发人员。 若要从设备绑定应用调用语音识别服务，请勿使用此 SDK。 （请使用其他客户端库或 REST API。）

若要使用 C# 服务库，请安装 [NuGet 包 Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/)。 有关库 API 参考，请参阅 [Microsoft 语音 C# 服务库](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)。

以下部分介绍如何使用 C# 服务库安装、构建和运行 C# 示例应用程序。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

以下示例是使用 [Visual Studio 2015 社区版](https://www.visualstudio.com/products/visual-studio-community-vs)为 Windows 8+ 和 .NET 4.5+ Framework 开发的。

### <a name="get-the-sample-application"></a>获取示例应用程序

从[语音 C# 服务库示例](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary)存储库克隆样本。

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>订阅语音识别 API，并获得免费试用的订阅密钥

语音 API 是认知服务（之前称为 Project Oxford）的一部分。 可以从[认知服务订阅](https://azure.microsoft.com/try/cognitive-services/)页面获取免费试用的订阅密钥。 选择语音 API 后，选择“获取 API 密钥”以获取密钥。 它将返回主密钥和辅助密钥。 两个密钥都绑定到相同的配额，因此可以使用任一密钥。

> [!IMPORTANT]
> * 获取订阅密钥。 必须拥有[订阅密钥](https://azure.microsoft.com/try/cognitive-services/)才能使用语音客户端库。
>
> * 使用订阅密钥。 使用提供的 C# 服务库示例应用程序，需要提供订阅密钥作为命令行参数之一。 有关详细信息，请参阅[运行示例应用程序](#step-3-run-the-sample-application)。

## <a name="step-1-install-the-sample-application"></a>步骤 1：安装示例应用程序

1. 启动 Visual Studio 2015 ，然后选择“文件” > “打开” > “项目/解决方案”。

2. 双击打开名为 SpeechClient.sln 的 Visual Studio 2015 解决方案 (.sln) 文件。 该解决方案随即在 Visual Studio 中打开。

## <a name="step-2-build-the-sample-application"></a>步骤 2：生成示例应用程序

按 Ctrl+Shift+B 或选择功能区菜单上的“生成”。 然后选择“生成解决方案”。

## <a name="step-3-run-the-sample-application"></a>步骤 3：运行示例应用程序

1. 生成完成后，按 F5 或选择功能区菜单上的“启动”来运行该示例。

2. 打开示例的输出目录，例如 SpeechClientSample\bin\Debug。 按下 Shift+右键单击，然后选择 “在此处打开命令窗口”。

3. 使用以下参数运行 `SpeechClientSample.exe`：

   * Arg[0]：指定输入音频 WAV 文件。
   * Arg[1]：指定音频区域设置。
   * Arg[2]：指定识别模式：Short 表示 `ShortPhrase` 模式，Long 表示 `LongDictation` 模式。
   * Arg[3]：指定用于访问语音识别服务的订阅密钥。

## <a name="samples-explained"></a>介绍的示例

### <a name="recognition-modes"></a>识别模式

* `ShortPhrase` 模式：最长 15 秒的语音。 当将数据发送到服务器时，客户端接收多个部分结果和一个最佳结果。
* `LongDictation` 模式：最长 10 分钟的语音。 当将数据发送到服务器时，基于服务器指示的语句停顿位置，客户端会接收到多个部分结果和多个最终结果。

### <a name="supported-audio-formats"></a>支持的音频格式

语音 API 通过使用以下编解码器支持音频/WAV：

* PCM 单通道
* Siren
* SirenSR

### <a name="preferences"></a>首选项

若要创建 SpeechClient，需要先创建一个 Preferences 对象。 Preferences 对象是一组用于配置语音服务行为的参数。 它由以下字段组成：

* `SpeechLanguage`：发送到语音服务的音频区域设置。
* `ServiceUri`：用于调用语音服务的终结点。
* `AuthorizationProvider`：用提取令牌以访问语音服务的 IAuthorizationProvider 实现。 虽然该示例提供了认知服务授权提供程序，但我们强烈建议你创建自己的实现来处理令牌缓存。
* `EnableAudioBuffering`：高级选项。 请参阅[连接管理](#connection-management)。

### <a name="speech-input"></a>语音输入

SpeechInput 对象包含两个字段：

* **音频**：SDK 从中提取音频的所选流实现。 它可以是支持读取的任何[流](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)。
   > [!NOTE]
   > 当流在读取中返回 0 时，SDK 会检测流的结束。

* **RequestMetadata**：有关语音请求的元数据。 有关详细信息，请参阅[参考](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)。

### <a name="speech-request"></a>语音请求

在实例化 SpeechClient 和 SpeechInput 对象后，使用 RecognizeAsync 向语音服务发出请求。

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

请求完成后，RecognizeAsync 返回的任务随即完成。 最后一个 RecognitionResult 是识别的结束。 如果服务或 SDK 意外失败，任务可能会失败。

### <a name="speech-recognition-events"></a>语音识别事件

#### <a name="partial-results-event"></a>部分结果事件

：每次语音服务预测你可能会说的内容时，甚至在你说完（如果使用 `MicrophoneRecognitionClient`）或完成发送数据（如果使用 `DataRecognitionClient`）之前，都会调用此事件。 可使用 `SpeechClient.SubscribeToPartialResult()` 订阅该事件。 或者可使用通用事件订阅方法 `SpeechClient.SubscribeTo<RecognitionPartialResult>()`。

**返回格式** | 说明 |
------|------
**LexicalForm** | 此形式最适合需要原始、未处理的语音识别结果的应用程序使用。
**DisplayText** | 应用了反向文本规范化、大写、标点符号和不当字词屏蔽的已识别短语。 在不当字词的初始字符后用星号进行了屏蔽，例如“d***”。 该形式最适合需要向用户显示语音识别结果的应用程序使用。
**Confidence** | 如语音识别服务器所定义，已识别短语表示的相关音频的置信度级别。
**MediaTime** | 相对于音频流开始时间的当前时间（以 100 纳秒为单位）。
**MediaDuration** | 相对于音频段的当前短语持续时间/长度（以 100 纳秒为单位）。

#### <a name="result-event"></a>结果事件
（在 `ShortPhrase` 模式下）完成说话后，将调用此事件。 针对结果提供了 n-best 选项。 在 `LongDictation` 模式下，可根据服务指示的语句停顿位置多次调用该事件。 可使用 `SpeechClient.SubscribeToRecognitionResult()` 订阅该事件。 或者可使用通用事件订阅方法 `SpeechClient.SubscribeTo<RecognitionResult>()`。

**返回格式** | 说明 |
------|------|
**RecognitionStatus** | 有关识别生成方式的状态。 例如，它是由于成功识别还是由于取消连接等原因而产生的。
**Phrases** | 具有识别置信度的 n-best 识别短语集。

有关识别结果的详细信息，请参阅[输出格式](../Concepts.md#output-format)。

### <a name="speech-recognition-response"></a>语音识别响应

语音响应示例：
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High) 
```

## <a name="connection-management"></a>连接管理

API 为每个请求使用单个 WebSocket 连接。 为了获得最佳用户体验，SDK 会尝试重新连接到语音服务，并从收到的最后一个 RecognitionResult 开始识别。 例如，如果音频请求时长为两分钟，SDK 在一分钟标记处收到 RecognitionEvent，但在五秒后发生网络故障，则 SDK 将启动从一分钟标记处开始的新连接。

>[!NOTE]
>SDK 不会返回一分钟标记处搜寻，因为该流可能不支持搜寻。 相反，SDK 会保留一个用于缓冲音频的内部缓冲区，并在收到 RecognitionResult 事件后清除缓冲区。

## <a name="buffering-behavior"></a>缓冲行为

默认情况下，SDK 会缓冲音频，以便它在发生网络中断时可以恢复。 有时会建议优先放弃网络断开期间丢失的音频并重启连接，在这种情况下，最好通过将 Preferences 对象中的 `EnableAudioBuffering` 设置为 `false` 来禁用音频缓冲。

## <a name="related-topics"></a>相关主题

[Microsoft 语音 C# 服务库参考](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
