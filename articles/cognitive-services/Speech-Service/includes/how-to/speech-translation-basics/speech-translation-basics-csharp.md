---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 0f5570f1c80fdecb4db6c8baf22ad05f1e4a32a6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266654"
---
## <a name="prerequisites"></a>先决条件

本文假定您具有 Azure 帐户和语音服务订阅。 如果您没有帐户和订阅，[请免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

在可以执行任何操作之前，您需要安装语音 SDK。 根据您的平台，请按照语音 SDK 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">"获取语音<span class="docon docon-navigate-external x-hidden-focus"></span>SDK"</a>部分下的说明进行操作。

## <a name="import-dependencies"></a>导入依赖项

要运行本文中的示例，请在*Program.cs*文件的顶部包括`using`以下语句。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感数据和环境变量

本文中的示例源代码取决于用于存储敏感数据的环境变量，例如语音资源订阅密钥和地区。 类`Program`包含从主机环境`static readonly string`变量分配的两个值，即`SPEECH__SUBSCRIPTION__KEY`和`SPEECH__SERVICE__REGION`。 这两个字段都在类作用域中，使它们在类的方法体中可访问。 有关环境变量的详细信息，请参阅[环境变量和应用程序配置](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
```

## <a name="create-a-speech-translation-configuration"></a>创建语音翻译配置

要使用语音 SDK 调用语音服务，您需要创建 一个[`SpeechTranslationConfig`][config]。 此类包括有关订阅的信息，例如密钥和相关区域、终结点、主机或授权令牌。

> [!TIP]
> 无论您执行语音识别、语音合成、翻译还是意图识别，您始终都会创建配置。

有几种方法可以初始化 ： [`SpeechTranslationConfig`][config]

* 使用订阅：在密钥和相关区域中传递。
* 使用终结点：在语音服务终结点中传递。 密钥或授权令牌是可选的。
* 与主机：在主机地址中传递。 密钥或授权令牌是可选的。
* 使用授权令牌：在授权令牌和相关区域中传递。

让我们来看看如何使用键和地区创建 。 [`SpeechTranslationConfig`][config] 请参阅[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页以查找区域标识符。

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>更改源语言

语音转换的一个常见任务是指定输入（或源）语言。 让我们来看看如何将输入语言更改为意大利语。 在代码中，与实例交互[`SpeechTranslationConfig`][config]，将分配给属性`SpeechRecognitionLanguage`。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

属性[`SpeechRecognitionLanguage`][recognitionlang]需要语言区域设置格式字符串。 您可以在受支持[区域设置/语言](../../../language-support.md)列表中的 **"区域设置"** 列中提供任何值。

## <a name="add-translation-language"></a>添加翻译语言

语音转换的另一个常见任务是指定目标翻译语言，至少需要一种，但支持多个语言。 在以下代码段中，法语和德语作为翻译语言的目标。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

每次调用[`AddTargetLanguage`][addlang]时，都会指定一种新的目标翻译语言。 换句话说，当从源语言中识别语音时，每个目标翻译都可以作为结果翻译操作的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻译识别器

创建 后，下一[`SpeechTranslationConfig`][config]步是初始化 。 [`TranslationRecognizer`][recognizer] 初始化 时[`TranslationRecognizer`][recognizer]，需要传递 您的`translationConfig`。 配置对象提供语音服务验证请求所需的凭据。

如果您使用设备的默认麦克风识别语音，则[`TranslationRecognizer`][recognizer]如下所示：

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

如果要指定音频输入设备，则需要在初始化 时[`AudioConfig`][audioconfig]`audioConfig`[`TranslationRecognizer`][recognizer]创建 并提供 参数。

> [!TIP]
> [了解如何获取音频输入设备的设备 ID。](../../../how-to-select-audio-input-devices.md)

首先，您将引用对象`AudioConfig`如下：

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

如果要提供音频文件而不是使用麦克风，仍需要提供`audioConfig`。 但是，当您创建 一[`AudioConfig`][audioconfig]个 时，`FromDefaultMicrophoneInput`而不是调用 ，您将`FromWavFileInput`调用并传递`filename`参数。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>翻译语音

要翻译语音，语音 SDK 依赖于麦克风或音频文件输入。 语音识别在语音转换之前发生。 所有对象都初始化后，调用识别一次函数并获取结果。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

有关语音到文本的详细信息，请参阅[语音识别的基础知识](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻译

在语音识别和翻译成功后，结果包含字典中的所有翻译。 字典[`Translations`][translations]键是目标翻译语言，值是翻译文本。 可识别的语音可以翻译，然后以不同的语言（语音到语音）合成。

### <a name="event-based-synthesis"></a>基于事件的合成

对象`TranslationRecognizer`公开事件`Synthesizing`。 该事件多次触发，并提供从转换识别结果中检索合成音频的机制。 如果要翻译成多种语言，请参阅[手动合成](#manual-synthesis)。 通过分配 分配 和[`VoiceName`][voicename]为`Synthesizing`事件提供事件处理程序来指定合成语音，获取音频。 下面的示例将翻译的音频保存为 *.wav*文件。

> [!IMPORTANT]
> 基于事件的合成仅适用于单个翻译，**不**添加多个目标翻译语言。 此外，[`VoiceName`][voicename]应与目标翻译语言的语言相同，例如;`"de"`可以映射到`"de-DE-Hedda"`。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>手动合成

该[`Translations`][translations]词典可用于从翻译文本中合成音频。 迭代每个翻译，并综合翻译。 创建`SpeechSynthesizer`实例时，`SpeechConfig`对象需要将其[`SpeechSynthesisVoiceName`][speechsynthesisvoicename]属性设置为所需的语音。 下面的示例转换为五种语言，然后每个翻译都合成到相应的神经语言中的音频文件。

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

有关语音合成的详细信息，请参阅[语音合成的基础知识](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
