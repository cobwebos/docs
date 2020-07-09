---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 60d5fb524d07590ed28641a6c91b87dbeb7dfc9f
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035745"
---
## <a name="prerequisites"></a>先决条件

本文假设已有一个 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，可[免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

需要先安装语音 SDK，然后才能执行任何操作。 根据你的平台，请按照_关于语音 sdk_一文的 " <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> 获取语音 sdk</a> " 一节中的说明进行操作。

## <a name="import-dependencies"></a>导入依赖项

若要运行本文中的示例，请在 *.Java  代码文件的顶部添加以下 `import` 语句。

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>敏感数据和环境变量

本文中的示例源代码依赖于用于存储敏感数据的环境变量，如语音资源订阅密钥和区域。 Java 代码文件包含从主机环境变量（即 `SPEECH__SUBSCRIPTION__KEY` 和 `SPEECH__SERVICE__REGION`）分配的两个 `static final String` 值。 这两个字段都在类范围内，因此可以在类的方法主体中访问它们。 有关环境变量的详细信息，请参阅[环境变量和应用程序配置](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>创建语音翻译配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechTranslationConfig`][config]。 此类包含有关订阅的信息，例如密钥和关联的区域、终结点、主机或授权令牌。

> [!TIP]
> 无论是要执行语音识别、语音合成、翻译还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechTranslationConfig`][config]：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

让我们看看如何使用密钥和区域创建 [`SpeechTranslationConfig`][config]。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页，找到你的区域标识符。

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>更改源语言

语音翻译的一项常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为意大利语。 在代码中与 [`SpeechTranslationConfig`][config] 实例交互，调用 `setSpeechRecognitionLanguage` 方法。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

[`setSpeechRecognitionLanguage`][recognitionlang] 函数需要语言区域设置格式字符串。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中“区域设置”列中的任何值  。

## <a name="add-translation-language"></a>添加翻译语言

语音翻译的另一项常见任务是指定目标翻译语言，至少需要一种语言，但支持多种语言。 以下代码片段将法语和德语设置为翻译语言目标。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

每次调用 [`addTargetLanguage`][addlang] 时，都会指定一种新的目标翻译语言。 换言之，根据源语言识别语音后，就会在接着进行的翻译操作过程中提供每项目标翻译。

## <a name="initialize-a-translation-recognizer"></a>初始化翻译识别器

创建 [`SpeechTranslationConfig`][config] 后，下一步是初始化 [`TranslationRecognizer`][recognizer]。 初始化 [`TranslationRecognizer`][recognizer] 时，需要向其传递 `translationConfig`。 配置对象会提供验证你的请求时语音服务所需的凭据。

如果使用设备的默认麦克风识别语音，则 [`TranslationRecognizer`][recognizer] 应如下所示：

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

如果要指定音频输入设备，则需要创建一个 [`AudioConfig`][audioconfig] 并在初始化 [`TranslationRecognizer`][recognizer] 时提供 `audioConfig` 参数。

> [!TIP]
> [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

首先，引用 `AudioConfig` 对象，如下所示：

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

如果要提供音频文件而不是使用麦克风，则仍需要提供 `audioConfig`。 但是，在创建 [`AudioConfig`][audioconfig]（而不是调用 `fromDefaultMicrophoneInput`）时，将调用 `fromWavFileInput` 并传递 `filename` 参数。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>翻译语音

为了翻译语音，语音 SDK 依赖于麦克风或音频文件输入。 在语音翻译之前先进行语音识别。 初始化所有对象后，调用识别一次的函数并获取结果。

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

有关语音转文本的详细信息，请参阅[语音识别基础知识](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻译

成功进行语音识别和翻译后，结果会包含字典中的所有翻译。 [`getTranslations`][translations] 函数会返回一个字典，其键是目标翻译语言，其值是已翻译的文本。 可以翻译已识别的语音，然后以另一种语言进行合成（语音转语音）。

### <a name="event-based-synthesis"></a>基于事件的合成

`TranslationRecognizer` 对象公开了 `synthesizing` 事件。 该事件触发多次，并提供一种从翻译识别结果检索合成音频的机制。 若要翻译为多种语言，请参阅[手动合成](#manual-synthesis)。 通过分配 [`setVoiceName`][voicename] 指定合成语音，并为 `synthesizing` 事件提供事件处理程序，获取音频。 以下示例将已翻译的音频另存为 .wav  文件。

> [!IMPORTANT]
> 基于事件的合成仅适用于单项翻译，请勿  添加多种目标翻译语言。 此外，[`setVoiceName`][voicename] 应与目标翻译语言相同（例如，`"de"` 可映射到 `"de-DE-Hedda"`）。

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>手动合成

[`getTranslations`][translations] 函数返回一个字典，可使用该字典从翻译文本合成音频。 循环访问每项翻译，并合成翻译。 创建 `SpeechSynthesizer` 实例时，`SpeechConfig` 对象需要将其 [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] 属性设为所需的语音。 以下示例翻译为五种语言，然后将每种翻译合成为相应神经语言的音频文件。

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

有关语音合成的详细信息，请参阅[语音合成基础知识](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
