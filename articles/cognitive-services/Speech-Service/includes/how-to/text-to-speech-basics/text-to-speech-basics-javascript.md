---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399562"
---
## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript 语音 SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a>，然后才能执行操作。 根据你的平台，使用以下说明：
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web 浏览器 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

另外，请根据目标环境使用以下项之一：

# <a name="import"></a>[import](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

有关 `import` 的详细信息，请参阅 <a href="https://javascript.info/import-export" target="_blank">export 和 import <span class="docon docon-navigate-external x-hidden-focus"></span></a>。

# <a name="require"></a>[require](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

有关 `require` 的详细信息，请参阅<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">什么是 require？<span class="docon docon-navigate-external x-hidden-focus"></span></a>。


# <a name="script"></a>[script](#tab/script)

下载并提取 <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript 语音 SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 文件，将其置于可供 HTML 文件访问的文件夹中。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 如果以 Web 浏览器为目标并使用 `<script>` 标记，则不需 `sdk` 前缀。 `sdk` 前缀是一个别名，用于为 `require` 模块命名。

---

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)。 此类包含有关你的订阅的信息，例如你的密钥和关联的区域、终结点、主机或授权令牌。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

在此示例中，你将使用订阅密钥和区域创建一个 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页以找到你的区域标识符。 此外，你将创建一些基本的样板代码，在本文的余下部分，你将修改这些代码以进行不同的自定义操作。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

接下来，创建一个 [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) 对象，用于执行文本到语音的转换，并将转换结果输出到扬声器、文件或其他输出流。 [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) 接受的参数包括上一步骤创建的 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) 对象，以及用于指定如何处理输出结果的 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) 对象。

若要开始，请创建一个 `AudioConfig`，以使用 `fromAudioFileOutput()` 静态函数自动将输出写入到 `.wav` 文件。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

接下来，实例化 `SpeechSynthesizer` 并将 `speechConfig` 对象和 `audioConfig` 对象作为参数传递。 然后，只需结合一个文本字符串运行 `speakTextAsync()`，就能执行语音合成和写入文件的操作。 结果回调是调用 `synthesizer.close()` 的理想位置，实际上，必须有此调用才能使合成正常运行。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

运行程序，合成的 `.wav` 文件将写入到指定的位置。 这只是最基本用法的一个很好示例。接下来，你将了解如何自定义输出，并将输出响应作为适用于自定义方案的内存中流进行处理。

## <a name="synthesize-to-speaker-output"></a>合成到扬声器输出

在某些情况下，你可能希望直接将合成的语音输出到扬声器。 为此，请使用 `fromDefaultSpeakerOutput()` 静态函数实例化 `AudioConfig`。 这会将语音输出到当前处于活动状态的输出设备。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>获取内存中流形式的结果

在许多语音应用程序开发方案中，你可能需要内存中流形式的最终音频数据，而不是直接写入文件。 这样便可以构建自定义行为，包括：

* 抽取生成的字节数组，作为自定义下游服务的可搜寻流。
* 将结果与其他 API 或服务相集成。
* 修改音频数据、写入自定义 `.wav` 标头，等等。

可以轻松地在前一个示例的基础上进行此项更改。 首先删除 `AudioConfig` 块，因为从现在起，你将手动管理输出行为，以提高控制度。 然后在 `SpeechSynthesizer` 构造函数中为 `AudioConfig` 传递 `undefined`。 

> [!NOTE]
> 如果为 `AudioConfig` 传递 `undefined`，而不是像在前面的扬声器输出示例中那样省略它，则默认不会在当前处于活动状态的输出设备上播放音频。

这一次，请将结果保存到 [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) 变量。 `SpeechSynthesisResult.audioData` 属性返回输出数据的 `ArrayBuffer`。 可以手动处理此 `ArrayBuffer`。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

在此处，可以使用生成的 `ArrayBuffer` 对象来实现任何自定义行为。

## <a name="customize-audio-format"></a>自定义音频格式

以下部分介绍如何自定义音频输出属性，包括：

* 音频文件类型
* 采样率
* 位深度

若要更改音频格式，请使用 `SpeechConfig` 对象的 `speechSynthesisOutputFormat` 属性。 此属性需要 [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) 类型的 `enum`，用于选择输出格式。 请参阅参考文档，获取可用的[音频格式列表](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)。

可根据要求对不同的文件类型使用不同的选项。 请注意，根据定义，`Raw24Khz16BitMonoPcm` 等原始格式不包括音频标头。 仅当你知道下游实现可以解码原始位流，或者你打算基于位深度、采样率、通道数等属性手动生成标头时，才使用原始格式。

此示例通过对 `SpeechConfig` 对象设置 `speechSynthesisOutputFormat` 来指定高保真 RIFF 格式 `Riff24Khz16BitMonoPcm`。 与上一部分中的示例类似，获取音频 `ArrayBuffer` 数据并与之进行交互。

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

再次运行程序会将 `.wav` 文件写入到指定的路径。

## <a name="use-ssml-to-customize-speech-characteristics"></a>使用 SSML 自定义语音特征

借助语音合成标记语言 (SSML)，可以通过从 XML 架构中提交请求，来微调文本转语音输出的音节、发音、语速、音量等特征。 本部分将演示一些实际用法示例，但如果你需要更详细的指导，请参阅 [SSML 操作指南文章](../../../speech-synthesis-markup.md)。

若要开始使用 SSML 进行自定义，请做出一项切换语音的简单更改。
首先，在根项目目录中为 SSML 配置创建一个新的 XML 文件，在本示例中为 `ssml.xml`。 根元素始终是 `<speak>`。将文本包装在 `<voice>` 元素中可以使用 `name` 参数来更改语音。 本示例将语音更改为英式英语男声语音。 请注意，此语音是标准语音，其定价和可用性与神经语音不同。   查看受支持标准语音的[完整列表](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices)。 

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

接下来，需要更改语音合成请求以引用 XML 文件。 该请求基本上保持不变，只不过需要使用 `speakSsmlAsync()` 而不是 `speakTextAsync()` 函数。 此函数需要 XML 字符串，因此，请先创建一个加载 XML 文件并将其作为字符串返回的函数。

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

有关 `readFileSync` 的详细信息，请参阅 <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js 文件系统<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在此处，结果对象与前面的示例完全相同。

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

输出正常，但可以做出几项简单的附加更改，使语音输出听起来更自然。 整体语速稍有点快，因此，我们将添加一个 `<prosody>` 标记，并将语速降至默认语速的 90%。  此外，句子中逗号后面的停顿稍有点短，听起来不太自然。 若要解决此问题，可添加一个 `<break>` 标记来延迟语音，然后将时间参数设置为 200ms。  重新运行合成，以查看这些自定义操作对输出的影响。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>神经语音

神经语音是立足于深度神经网络的语音合成算法。 使用神经语音时，几乎无法将合成的语音与人类录音区分开来。 随着类人的自然韵律和字词的清晰发音，用户在与 AI 系统交互时，神经语音显著减轻了听力疲劳。

若要切换到某种神经语音，请将 `name` 更改为[神经语音选项](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)之一。 然后，为 `mstts` 添加 XML 命名空间，并在 `<mstts:express-as>` 标记中包装文本。 使用 `style` 参数自定义讲话风格。 此示例使用 `cheerful`，但请尝试将其设置为 `customerservice` 或 `chat`，以了解讲话风格的差别。

> [!IMPORTANT]
> 只有“美国东部”、“东南亚”和“西欧”区域中创建的语音资源才支持神经语音。    

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
