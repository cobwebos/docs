---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.openlocfilehash: 379a590d2e8327d8c2788287c5cf8606fda334c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090887"
---
## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript 语音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>，然后才能执行操作。 根据你的平台，使用以下说明：
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web 浏览器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

另外，请根据目标环境使用以下项之一：

# <a name="script"></a>[script](#tab/script)

下载并提取 <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript 语音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a> microsoft.cognitiveservices.speech.sdk.bundle.js 文件，将其置于可供 HTML 文件访问的文件夹中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果以 Web 浏览器为目标并使用 `<script>` 标记，则不需 `sdk` 前缀。 `sdk` 前缀是一个别名，用于为 `require` 模块命名。

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

有关 `import` 的详细信息，请参阅 <a href="https://javascript.info/import-export" target="_blank">export 和 import <span class="docon docon-navigate-external x-hidden-focus"></span></a>。

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

有关 `require` 的详细信息，请参阅<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什么是 require？<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

---

## <a name="create-a-translation-configuration"></a>创建翻译配置

若要使用 Speech SDK 调用翻译服务，需要创建 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) 。 此类包含有关你的订阅的信息，例如你的密钥和关联的区域、终结点、主机或授权令牌。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。
可以通过以下几种方法初始化 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest)：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

让我们看看如何使用密钥和区域创建 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest)。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页，找到你的区域标识符。

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>初始化转换器

创建 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) 后，下一步是初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest)。 初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 时，需要向其传递 `speechTranslationConfig`。 这将提供翻译服务验证请求所需的凭据。

如果要翻译通过设备的默认麦克风提供的语音，以下内容应如下所 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 示：

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

如果要指定音频输入设备，则需要创建一个 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) 并在初始化 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 时提供 `audioConfig` 参数。

> [!TIP]
> [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。
引用 `AudioConfig` 对象，如下所示：

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

如果要提供音频文件而不是使用麦克风，则仍需要提供 `audioConfig`。 但是，只有在以 Node.js 为目标时才能这样做。创建 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) 时，需调用 `fromWavFileOutput` 并传递 `filename` 参数，而不是调用 `fromDefaultMicrophoneInput`。

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>翻译语音

用于 JavaScript 的语音 SDK 的[TranslationRecognizer 类](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest)公开了几种可用于语音转换的方法。

* 单步转换（async）-以非阻止（异步）模式执行转换。 这将转换单个查询文本。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。
* 连续转换（async）-异步启动连续转换操作。 用户向事件注册并处理各种应用程序状态。 若要停止异步连续转换，请调用 [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync) 。

> [!NOTE]
> 详细了解如何[选择语音识别模式](../../../how-to-choose-recognition-mode.md)。
### <a name="specify-a-target-language"></a>指定目标语言

若要进行转换，必须指定源语言和至少一种目标语言。
您可以使用[语音转换表](../../../language-support.md#speech-translation)中列出的区域设置来选择源语言。 在同一链接上查找翻译语言的选项。 当您想要查看文本，或想要收听合成翻译的语音时，适用于目标语言的选项有所不同。 若要从英语转换为德语，请修改翻译配置对象：

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>单步识别

下面是使用进行异步单次转换的示例 [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognizeonceasync) ：

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

需要编写一些代码来处理结果。 此示例将计算 [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult?view=azure-node-latest) 转换为德语的：

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

你的代码还可以处理翻译处理时提供的更新。
你可以使用这些更新来提供有关翻译进度的视觉反馈。
有关显示在转换过程中提供的更新的示例代码，请参阅[此 JavaScript Node.js 示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js)。 下面的代码还显示翻译过程中生成的详细信息。

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>连续翻译

连续翻译比单步识别更多。 它要求你订阅 `recognizing`、`recognized` 和 `canceled` 事件以获取识别结果。 若要停止转换，必须调用 [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync) 。 下面是如何在音频输入文件中执行连续转换的示例。

首先，我们将定义输入并初始化一个 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest)：

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

我们将订阅从 [`TranslationRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest) 发送的事件。

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognizing)：包含中间转换结果的事件的信号。
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#recognized)：包含最终转换结果（表示成功的转换尝试）的事件信号。
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#sessionstopped)：指示转换会话结束（操作）的事件信号。
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer?view=azure-node-latest#canceled)：包含已取消转换结果的事件的信号（表示已取消作为结果的转换尝试或直接取消请求，或者传输或协议失败）。

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

完成所有设置后，可以调用 [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#startcontinuousrecognitionasync)。

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>选择源语言

语音翻译的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中查找 [`SpeechTranslationConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest) ，然后在其下直接添加以下行。

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig?view=azure-node-latest#speechrecognitionlanguage) 属性需要语言区域设置格式字符串。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中“区域设置”列中的任何值。

## <a name="choose-one-or-more-target-languages"></a>选择一种或多种目标语言

语音 SDK 可以并行转换为多个目标语言。 可用的目标语言与源语言列表有些不同，您使用语言代码（而不是区域设置）指定目标语言。
请参阅[语言支持页上的 "语音转换" 表](../../../language-support.md#speech-translation)中的文本目标语言的列表。 你还可以在此处找到有关转换为合成语言的详细信息。

以下代码将德语添加为目标语言：

```javascript
translationConfig.addTargetLanguage("de");
```

由于有多种目标语言翻译，你的代码在检查结果时必须指定目标语言。 下面的代码获取德语的转换结果。

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```
