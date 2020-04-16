---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399595"
---
## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。 根据您的平台，请按照语音 SDK 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">"获取语音<span class="docon docon-navigate-external x-hidden-focus"></span>SDK"</a>部分下的说明进行操作。

## <a name="import-dependencies"></a>导入依赖项

要运行本文中的示例，请在C++代码文件的顶部`#include`包括`using`以下内容和语句。

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感数据和环境变量

本文中的示例源代码取决于用于存储敏感数据的环境变量，例如语音资源订阅密钥和地区。 C++代码文件包含从主机环境变量（即`SPEECH__SUBSCRIPTION__KEY`和`SPEECH__SERVICE__REGION`） 分配的两个字符串值。 这两个字段都在类作用域中，使它们在类的方法体中可访问。 有关环境变量的详细信息，请参阅[环境变量和应用程序配置](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
```

## <a name="create-a-speech-translation-configuration"></a>创建语音翻译配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechTranslationConfig`][config]。 此类包含有关你的订阅的信息，例如你的密钥和关联的区域、终结点、主机或授权令牌。

> [!TIP]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechTranslationConfig`][config]：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

让我们看看如何使用密钥和区域创建 [`SpeechTranslationConfig`][config]。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页以找到你的区域标识符。

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>更改源语言

语音转换的一个常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中，与实例交互[`SpeechTranslationConfig`][config]，调用`SetSpeechRecognitionLanguage`方法。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

[`SpeechRecognitionLanguage`][recognitionlang] 属性需要语言区域设置格式字符串。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中“区域设置”列中的任何值  。

## <a name="add-translation-language"></a>添加翻译语言

语音转换的另一个常见任务是指定目标翻译语言，至少需要一种，但支持多个语言。 在以下代码段中，法语和德语作为翻译语言的目标。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

每次调用[`AddTargetLanguage`][addlang]时，都会指定一种新的目标翻译语言。 换句话说，当从源语言中识别语音时，每个目标翻译都可以作为结果翻译操作的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻译识别器

创建 [`SpeechTranslationConfig`][config] 后，下一步是初始化 [`TranslationRecognizer`][recognizer]。 初始化 [`TranslationRecognizer`][recognizer] 时，需要向其传递 `translationConfig`。 配置对象提供语音服务验证请求所需的凭据。

如果使用设备的默认麦克风识别语音，则 [`TranslationRecognizer`][recognizer] 应如下所示：

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

如果要指定音频输入设备，则需要创建一个 [`AudioConfig`][audioconfig] 并在初始化 [`TranslationRecognizer`][recognizer] 时提供 `audioConfig` 参数。

> [!TIP]
> [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

首先，您将引用对象`AudioConfig`如下：

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

如果要提供音频文件而不是使用麦克风，则仍需要提供 `audioConfig`。 但是，当你创建 [`AudioConfig`][audioconfig]（而不是调用 `FromDefaultMicrophoneInput`）时，你将调用 `FromWavFileInput` 并传递 `filename` 参数。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>翻译语音

要翻译语音，语音 SDK 依赖于麦克风或音频文件输入。 语音识别在语音转换之前发生。 所有对象都初始化后，调用识别一次函数并获取结果。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

有关语音到文本的详细信息，请参阅[语音识别的基础知识](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻译

在语音识别和翻译成功后，结果包含字典中的所有翻译。 字典[`Translations`][translations]键是目标翻译语言，值是翻译文本。 可识别的语音可以翻译，然后以不同的语言（语音到语音）合成。

### <a name="event-based-synthesis"></a>基于事件的合成

对象`TranslationRecognizer`公开事件`Synthesizing`。 该事件多次触发，并提供从转换识别结果中检索合成音频的机制。 如果要翻译成多种语言，请参阅[手动合成](#manual-synthesis)。 通过分配 分配 和[`SetVoiceName`][voicename]为`Synthesizing`事件提供事件处理程序来指定合成语音，获取音频。 下面的示例将翻译的音频保存为 *.wav*文件。

> [!IMPORTANT]
> 基于事件的合成仅适用于单个翻译，**不**添加多个目标翻译语言。 此外，[`SetVoiceName`][voicename]应与目标翻译语言的语言相同，例如;`"de"`可以映射到`"de-DE-Hedda"`。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>手动合成

该[`Translations`][translations]词典可用于从翻译文本中合成音频。 迭代每个翻译，并综合翻译。 创建`SpeechSynthesizer`实例时，`SpeechConfig`对象需要将其[`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename]属性设置为所需的语音。 下面的示例转换为五种语言，然后每个翻译都合成到相应的神经语言中的音频文件。

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

有关语音合成的详细信息，请参阅[语音合成的基础知识](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
