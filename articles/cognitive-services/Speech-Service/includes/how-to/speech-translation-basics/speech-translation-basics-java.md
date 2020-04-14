---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 9f1fdca94edc6d3938c137185e037ba0c57b7a0f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266610"
---
## <a name="prerequisites"></a>先决条件

本文假定您具有 Azure 帐户和语音服务订阅。 如果您没有帐户和订阅，[请免费试用语音服务](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

在可以执行任何操作之前，您需要安装语音 SDK。 根据您的平台，请按照语音 SDK 文章的<a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">"获取语音<span class="docon docon-navigate-external x-hidden-focus"></span>SDK"</a>部分下的说明进行操作。

## <a name="import-dependencies"></a>导入依赖项

要运行本文中的示例，请在 * 顶部`import`包括以下语句 *。Java*代码文件。

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

本文中的示例源代码取决于用于存储敏感数据的环境变量，例如语音资源订阅密钥和地区。 Java 代码文件包含从主机`static final String`环境变量分配的两个值，即`SPEECH__SUBSCRIPTION__KEY`和`SPEECH__SERVICE__REGION`。 这两个字段都在类作用域中，使它们在类的方法体中可访问。 有关环境变量的详细信息，请参阅[环境变量和应用程序配置](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)。

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
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

语音转换的一个常见任务是指定输入（或源）语言。 让我们来看看如何将输入语言更改为意大利语。 在代码中，与实例交互[`SpeechTranslationConfig`][config]，调用`setSpeechRecognitionLanguage`方法。

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

函数[`setSpeechRecognitionLanguage`][recognitionlang]需要语言区域设置格式字符串。 您可以在受支持[区域设置/语言](../../../language-support.md)列表中的 **"区域设置"** 列中提供任何值。

## <a name="add-translation-language"></a>添加翻译语言

语音转换的另一个常见任务是指定目标翻译语言，至少需要一种，但支持多个语言。 在以下代码段中，法语和德语作为翻译语言的目标。

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

每次调用[`addTargetLanguage`][addlang]时，都会指定一种新的目标翻译语言。 换句话说，当从源语言中识别语音时，每个目标翻译都可以作为结果翻译操作的一部分。

## <a name="initialize-a-translation-recognizer"></a>初始化翻译识别器

创建 后，下一[`SpeechTranslationConfig`][config]步是初始化 。 [`TranslationRecognizer`][recognizer] 初始化 时[`TranslationRecognizer`][recognizer]，需要传递 您的`translationConfig`。 配置对象提供语音服务验证请求所需的凭据。

如果您使用设备的默认麦克风识别语音，则[`TranslationRecognizer`][recognizer]如下所示：

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

如果要指定音频输入设备，则需要在初始化 时[`AudioConfig`][audioconfig]`audioConfig`[`TranslationRecognizer`][recognizer]创建 并提供 参数。

> [!TIP]
> [了解如何获取音频输入设备的设备 ID。](../../../how-to-select-audio-input-devices.md)

首先，您将引用对象`AudioConfig`如下：

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

如果要提供音频文件而不是使用麦克风，仍需要提供`audioConfig`。 但是，当您创建 一[`AudioConfig`][audioconfig]个 时，`fromDefaultMicrophoneInput`而不是调用 ，您将`fromWavFileInput`调用并传递`filename`参数。

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

要翻译语音，语音 SDK 依赖于麦克风或音频文件输入。 语音识别在语音转换之前发生。 所有对象都初始化后，调用识别一次函数并获取结果。

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

有关语音到文本的详细信息，请参阅[语音识别的基础知识](../../../speech-to-text-basics.md)。

## <a name="synthesize-translations"></a>合成翻译

在语音识别和翻译成功后，结果包含字典中的所有翻译。 函数[`getTranslations`][translations]返回一个字典，其中键作为目标翻译语言，值是翻译的文本。 可识别的语音可以翻译，然后以不同的语言（语音到语音）合成。

### <a name="event-based-synthesis"></a>基于事件的合成

对象`TranslationRecognizer`公开事件`synthesizing`。 该事件多次触发，并提供从转换识别结果中检索合成音频的机制。 如果要翻译成多种语言，请参阅[手动合成](#manual-synthesis)。 通过分配 分配 和[`setVoiceName`][voicename]为`synthesizing`事件提供事件处理程序来指定合成语音，获取音频。 下面的示例将翻译的音频保存为 *.wav*文件。

> [!IMPORTANT]
> 基于事件的合成仅适用于单个翻译，**不**添加多个目标翻译语言。 此外，[`setVoiceName`][voicename]应与目标翻译语言的语言相同，例如;`"de"`可以映射到`"de-DE-Hedda"`。

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

函数[`getTranslations`][translations]返回可用于从翻译文本合成音频的字典。 迭代每个翻译，并综合翻译。 创建`SpeechSynthesizer`实例时，`SpeechConfig`对象需要将其[`setSpeechSynthesisVoiceName`][speechsynthesisvoicename]属性设置为所需的语音。 下面的示例转换为五种语言，然后每个翻译都合成到相应的神经语言中的音频文件。

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

有关语音合成的详细信息，请参阅[语音合成的基础知识](../../../text-to-speech-basics.md)。

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
