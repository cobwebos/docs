---
title: 通过 Azure 上的自定义语音服务使用自定义语音终结点 | Microsoft Docs
description: 了解如何通过认知服务中的自定义语音服务使用自定义语音转文本终结点。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: d28065d7962ee660cafd4b3321abdd6a8f94abcb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365538"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>使用自定义语音转文本终结点
可以向 Azure 自定义语音服务语音转文本终结点发送请求，方式与向默认认知服务语音终结点发送请求类似。 这些终结点在功能上与语音 API 的默认终结点相同。 因此，通过语音 API 的客户端库或 REST API 提供的功能也适用于自定义终结点。

使用此服务创建的终结点可以处理不同数量的并发请求。 卷取决于与订阅关联的定价层。 如果接收请求过多，则会出现错误。 免费层每月的请求数量有一定限制。

服务假定实时传输数据。 如果发送速度更快，请求将被视为运行，直到其音频实时的时长已过。

> [!NOTE]
> 我们还支持[新的 Web 套接字](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol)。 如果计划通过自定义语音终结点使用 Web 套接字，请遵循此处的说明进行操作。
>
> 即将推出新的 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) 支持。 如果计划通过 HTTP 调用自定义语音终结点，请遵循此处的说明进行操作。
>

## <a name="send-requests-by-using-the-speech-client-library"></a>使用语音客户端库发送请求

若要使用语音客户端库将请求发送到自定义终结点，请启动识别客户端。 使用来自 [NuGet](http://nuget.org/) 的客户端语音 SDK。 搜索语音识别，并从 Microsoft 选择适用于平台的语音识别包。 一些示例代码可在 [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows) 上找到。 客户端语音 SDK 提供的工厂类 SpeechRecognitionServiceFactory 提供以下方法：

  *   ```CreateDataClient(...)```：数据识别客户端。
  *   ```CreateDataClientWithIntent(...)```：数据意向识别客户端。
  *   ```CreateMicrophoneClient(...)```：麦克风识别客户端。
  *   ```CreateMicrophoneClientWithIntent(...)```：麦克风意向识别客户端。

有关详细文档，请参阅[必应语音 API](https://docs.microsoft.com/azure/cognitive-services/speech/home)。 自定义语音服务终结点支持相同 SDK。

数据识别客户端适用于数据语音识别，例如文件或其他音频源。 麦克风识别客户端适用于来自麦克风的语音识别。 如果已为方案生成 LUIS 应用程序，则在任一客户端使用意向都可从[语言理解智能服务](https://www.luis.ai/) (LUIS) 返回结构化意向结果。

四种类型的客户端都可以通过两种方式实例化。 第一种方式使用标准的认知服务语音 API。 第二种方式可以指定一个 URL，它对应通过自定义语音服务创建的自定义终结点。

例如，可以创建一个 DataRecognitionClient，它使用以下命令向自定义终结点发送请求：

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

your_subscriptionId 和 endpointURL 分别指代“开发信息”页面上的订阅密钥和 Web 套接字 URL。

AuthenticationUri 用来接收身份验证服务令牌。 必须单独设置此 URI，如以下示例代码中所示。

此示例代码演示如何使用客户端 SDK：

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> 在 SDK 中使用“创建”方法时，由于“创建”方法重载，必须提供两次订阅 ID。
>

自定义语音服务针对短格式和长格式识别使用两种不同的 URL。 两种都列在“开发”页上。 针对希望使用的特定格式使用正确的终结点 URL。

如需深入了解使用自定义终结点调用多种识别客户端的信息，请参阅 [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop) 类。 此页上的文档引用声学模型适应，但可应用到所有使用自定义语音服务创建的终结点。

## <a name="send-requests-by-using-the-speech-protocol"></a>使用语音协议发送请求

[语音协议](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol)显示的终结点为开源 Web 套接字协议终结点。

目前，唯一的正式客户端实现仅适用于 [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)。 如果希望从提供的示例入门，请进行以下更改以再次编码和生成示例：

1. 在 src\sdk\speech.browser\SpeechConnectionFactory.ts 中，将主机名“wss://speech.platform.bing.com”替换为部署详细信息页上显示的主机名。 请勿在此处插入完整的 URI，仅插入 wss 协议方案和主机名。 例如：

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. 按要求在 samples\browser\Samples.html 中设置 recognitionMode 参数：
    * RecognitionMode.Interactive 最长支持 15 秒的请求。
    * RecognitionMode.Conversation 和 RecognitionMode.Dictation（两者在自定义语音服务中等效）支持长达 10 分钟的请求。

3. 在使用前使用“gulp build”生成示例。

> [!NOTE]
> 确保对此协议使用正确的 URI。 所需的方案为 wss（并非客户端协议中所用的 http）。 

有关详细信息，请参阅[必应语音 API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries) 文档。

## <a name="send-requests-by-using-http"></a>使用 HTTP 发送请求

使用 HTTP post 将请求发送到自定义终结点类似于通过 HTTP 将请求发送到认知服务必应语音 API。 修改 URL 以反映自定义部署的地址。

通过 HTTP 发送的针对认知服务语音终结点和使用此服务创建的自定义终结点的请求有一些限制。 HTTP 请求不能在识别过程中返回部分结果。 此外，音频内容的请求持续时间限制在 10 秒，总体持续时间限制在 14 秒。

若要创建 post 请求，请按照与认知服务语音 API 相同的过程进行操作。

1. 使用订阅 ID 获取访问令牌。 需要此令牌才能访问识别终结点。 可以重复使用此令牌 10 分钟。

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      subscriptionId 应设置为此部署使用的订阅 ID。 响应是下一个请求所需的纯令牌。

2. 再次使用 POST 将音频发布到终结点。

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    “token”是通过上一个调用接收的访问令牌。 https_endpoint 是自定义语音转文本终结点的完整地址，显示在“部署信息”页面上。

有关 HTTP post 参数和响应格式的详细信息，请参阅 [Microsoft 识别服务必应语音 HTTP API](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation)。

## <a name="send-requests-by-using-the-service-library"></a>使用服务库发送请求
通过服务库，服务可以利用 Microsoft 语音听录云实时将语音转换为文本，这样客户端应用便可以同步和异步方式发送音频并接受返回的部分识别结果。 可在[此处](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)找到服务 SDK 的详细信息

> [!NOTE]
> 使用服务库时，必须将 IAuthorizationProvider 实现中的授权提供程序 URI 更改为 https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken。

## <a name="next-steps"></a>后续步骤
* 通过[自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)提高准确性。
* 通过[自定义语言模型](cognitive-services-custom-speech-create-language-model.md)提高准确性。
