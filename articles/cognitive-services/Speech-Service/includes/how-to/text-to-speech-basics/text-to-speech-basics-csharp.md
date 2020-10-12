---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e2631332be1ea2a9e63755ea53897ba0d7813ef
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332413"
---
本快速入门介绍使用语音 SDK 进行文本到语音合成的常见设计模式。 首先，请进行基本的配置和合成，然后通过更高级的示例来了解自定义应用程序开发，其中包括：

* 获取内存中流形式的响应
* 自定义输出采样率和比特率
* 使用 SSML（语音合成标记语言）提交合成请求
* 使用神经语音

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [C# 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech)。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，使用以下说明：

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在脚本的最前面包含以下 `using` 语句。

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true)。 此类包含有关你的订阅的信息，例如你的密钥和关联的区域、终结点、主机或授权令牌。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true)：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

在此示例中，你将使用订阅密钥和区域创建一个 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true)。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页以找到你的区域标识符。 此外，你将创建一些基本的样板代码，在本文的余下部分，你将修改这些代码以进行不同的自定义操作。

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

接下来，创建一个 [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet&preserve-view=true) 对象，用于执行文本到语音的转换，并将转换结果输出到扬声器、文件或其他输出流。 [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet&preserve-view=true) 接受的参数包括上一步骤创建的 [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true) 对象，以及用于指定如何处理输出结果的 [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet&preserve-view=true) 对象。

若要开始，请创建一个 `AudioConfig` 以使用 `FromWavFileOutput()` 函数自动将输出写入 `.wav` 文件，并使用 `using` 语句将其实例化。 此上下文中的 `using` 语句会自动释放非托管资源，导致对象在释放后超出范围。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

接下来，使用另一个 `using` 语句实例化 `SpeechSynthesizer`。 将 `config` 对象和 `audioConfig` 对象作为参数进行传递。 然后，只需结合一个文本字符串运行 `SpeakTextAsync()`，就能执行语音合成和写入文件的操作。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

运行程序，合成的 `.wav` 文件会写入指定的位置。 这只是最基本用法的一个很好示例。接下来，你将了解如何自定义输出，并将输出响应作为适用于自定义方案的内存中流进行处理。

## <a name="synthesize-to-speaker-output"></a>合成到扬声器输出

在某些情况下，你可能希望直接将合成的语音输出到扬声器。 为此，只需在上述示例中创建 `SpeechSynthesizer` 时省略 `AudioConfig` 参数即可。 这会将语音输出到当前处于活动状态的输出设备。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>获取内存中流形式的结果

在许多语音应用程序开发方案中，你可能需要内存中流形式的最终音频数据，而不是直接写入文件。 这样便可以构建自定义行为，包括：

* 抽取生成的字节数组，作为自定义下游服务的可搜寻流。
* 将结果与其他 API 或服务相集成。
* 修改音频数据、写入自定义 `.wav` 标头，等等。

可以轻松地在前一个示例的基础上进行此项更改。 首先删除 `AudioConfig` 块，因为从现在起，你将手动管理输出行为，以提高控制度。 然后在 `SpeechSynthesizer` 构造函数中为 `AudioConfig` 传递 `null`。 

> [!NOTE]
> 如果为 `AudioConfig` 传递 `null`，而不是像在前面的扬声器输出示例中那样省略它，则默认不会在当前处于活动状态的输出设备上播放音频。

这一次，请将结果保存到 [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet&preserve-view=true) 变量。 `AudioData` 属性包含输出数据的 `byte []`。 可以手动使用此 `byte []`，也可以使用 [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet&preserve-view=true) 类来管理内存中流。 此示例使用 `AudioDataStream.FromResult()` 静态函数从结果中获取流。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

在此处，可以使用生成的 `stream` 对象来实现任何自定义行为。

## <a name="customize-audio-format"></a>自定义音频格式

以下部分介绍如何自定义音频输出属性，包括：

* 音频文件类型
* 采样率
* 位深度

若要更改音频格式，请对 `SpeechConfig` 对象使用 `SetSpeechSynthesisOutputFormat()` 函数。 此函数需要 [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet&preserve-view=true) 类型的 `enum`，用于选择输出格式。 请参阅参考文档，获取可用的[音频格式列表](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet&preserve-view=true)。

可根据要求对不同的文件类型使用不同的选项。 请注意，根据定义，`Raw24Khz16BitMonoPcm` 等原始格式不包括音频标头。 仅当你知道下游实现可以解码原始位流，或者你打算基于位深度、采样率、通道数等属性手动生成标头时，才使用原始格式。

此示例通过对 `SpeechConfig` 对象设置 `SpeechSynthesisOutputFormat` 来指定高保真 RIFF 格式 `Riff24Khz16BitMonoPcm`。 类似于上一部分中的示例，可以使用 [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet&preserve-view=true) 获取结果的内存中流，然后将其写入文件。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

再次运行程序会将 `.wav` 文件写入指定的路径。

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

接下来，需要更改语音合成请求以引用 XML 文件。 该请求基本上保持不变，只不过需要使用 `SpeakSsmlAsync()` 而不是 `SpeakTextAsync()` 函数。 此函数需要 XML 字符串，因此，请先使用 `File.ReadAllText()` 加载字符串形式的 SSML 配置。 在此处，结果对象与前面的示例完全相同。

> [!NOTE]
> 如果使用的是 Visual Studio，则生成配置默认可能不会查找 XML 文件。 若要解决此问题，请右键单击 XML 文件并选择“属性”。  将“生成操作”更改为“内容”，将“复制到输出目录”更改为“始终复制”。    

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
