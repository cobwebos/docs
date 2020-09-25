---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: b8cbbbb24293a3ae931a55299a3a97411e74c910
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326884"
---
语音服务的核心功能之一是能够识别人类语音并将其翻译成其他语言。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音翻译。 此快速入门介绍以下主题：

* 将语音翻译为文本
* 将语音翻译为多种目标语言
* 直接进行语音转语音翻译

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [c + + 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text) 。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，按照“关于语音 SDK”一文的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">获取语音 SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a> 部分中的说明进行操作。

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在 C++ 代码文件的顶部包含以下 `#include` 和 `using` 语句。

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

本文中的示例源代码依赖于用于存储敏感数据的环境变量，如语音资源订阅密钥和区域。 C++ 代码文件包含从主机环境变量（即 `SPEECH__SUBSCRIPTION__KEY` 和 `SPEECH__SERVICE__REGION`）分配的两个字符串值。 这两个字段都在类范围内，因此可以在类的方法主体中访问它们。 有关环境变量的详细信息，请参阅[环境变量和应用程序配置](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

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

让我们看看如何使用密钥和区域创建 [`SpeechTranslationConfig`][config]。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页，找到你的区域标识符。

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

语音翻译的一项常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中与 [`SpeechTranslationConfig`][config] 实例交互，调用 `SetSpeechRecognitionLanguage` 方法。

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

语音翻译的另一项常见任务是指定目标翻译语言，至少需要一种语言，但支持多种语言。 以下代码片段将法语和德语设置成了目标翻译语言。

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

每次调用 [`AddTargetLanguage`][addlang] 时，都会指定一种新的目标翻译语言。 换言之，根据源语言识别语音后，就会在接着进行的翻译操作过程中提供每项目标翻译。

## <a name="initialize-a-translation-recognizer"></a>初始化翻译识别器

创建 [`SpeechTranslationConfig`][config] 后，下一步是初始化 [`TranslationRecognizer`][recognizer]。 初始化 [`TranslationRecognizer`][recognizer] 时，需要向其传递 `translationConfig`。 配置对象会提供验证你的请求时语音服务所需的凭据。

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

首先，引用 `AudioConfig` 对象，如下所示：

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

如果要提供音频文件而不是使用麦克风，则仍需要提供 `audioConfig`。 但是，在创建 [`AudioConfig`][audioconfig]（而不是调用 `FromDefaultMicrophoneInput`）时，将调用 `FromWavFileInput` 并传递 `filename` 参数。

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

为了翻译语音，语音 SDK 依赖于麦克风或音频文件输入。 在语音翻译之前先进行语音识别。 初始化所有对象后，调用识别一次的函数并获取结果。

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

有关语音转文本的详细信息，请参阅[语音识别基础知识](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻译

成功进行语音识别和翻译后，结果会包含字典中的所有翻译。 [`Translations`][translations] 字典键是目标翻译语言，其值是已翻译的文本。 可以翻译已识别的语音，然后以另一种语言进行合成（语音转语音）。

### <a name="event-based-synthesis"></a>基于事件的合成

`TranslationRecognizer` 对象公开了 `Synthesizing` 事件。 该事件触发多次，并提供一种从翻译识别结果检索合成音频的机制。 若要翻译为多种语言，请参阅[手动合成](#manual-synthesis)。 通过分配 [`SetVoiceName`][voicename] 指定合成语音，并为 `Synthesizing` 事件提供事件处理程序，获取音频。 以下示例将已翻译的音频另存为 .wav  文件。

> [!IMPORTANT]
> 基于事件的合成仅适用于单项翻译，请勿  添加多种目标翻译语言。 此外，[`SetVoiceName`][voicename] 应与目标翻译语言相同（例如，`"de"` 可映射到 `"de-DE-Hedda"`）。

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

[`Translations`][translations] 字典可用于从翻译文本合成音频。 循环访问每项翻译，并合成翻译。 创建 `SpeechSynthesizer` 实例时，`SpeechConfig` 对象需要将其 [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] 属性设为所需的语音。 以下示例翻译为五种语言，然后将每种翻译合成为相应神经语言的音频文件。

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

有关语音合成的详细信息，请参阅[语音合成基础知识](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
