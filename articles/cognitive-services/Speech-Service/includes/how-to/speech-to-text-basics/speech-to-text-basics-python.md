---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 92fb6bb470c6ab9c1a8f1f0542c26a8fef07e6b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376582"
---
语音服务的核心功能之一是能够识别并转录人类语音（通常称为语音转文本）。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音转文本转换。

## <a name="skip-to-samples-on-github"></a>跳转到 GitHub 上的示例

如果要直接跳到示例代码，请参阅 GitHub 上的 [Python 快速入门示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python)。

## <a name="prerequisites"></a>先决条件

本文假设：

* 你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-and-import-the-speech-sdk"></a>安装和导入语音 SDK

你需要先安装语音 SDK，然后才能执行任何操作。

```Python
pip install azure-cognitiveservices-speech
```

如果使用的是 macOS 且你遇到安装问题，则可能需要先运行此命令。

```Python
python3 -m pip install --upgrade pip
```

安装语音 SDK 后，将其导入到 Python 项目中，其中包含此语句。

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>创建语音配置

若要使用语音 SDK 调用语音服务，需要创建 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)。 此类包含有关你的订阅的信息，例如你的密钥和关联的区域、终结点、主机或授权令牌。

> [!NOTE]
> 无论你是要执行语音识别、语音合成、翻译，还是意向识别，都需要创建一个配置。

可以通过以下几种方法初始化 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)：

* 使用订阅：传入密钥和关联的区域。
* 使用终结点：传入语音服务终结点。 密钥或授权令牌是可选的。
* 使用主机：传入主机地址。 密钥或授权令牌是可选的。
* 使用授权令牌：传入授权令牌和关联的区域。

让我们看看如何使用密钥和区域创建 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)。 请查看[区域支持](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)页以找到你的区域标识符。

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>初始化识别器

创建 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) 后，下一步是初始化 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)。 初始化 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) 时，向其传递 `speech_config`。 这会提供语音服务验证请求所需的凭据。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

## <a name="recognize-from-microphone-or-file"></a>从麦克风或文件识别

如果要指定音频输入设备，则需要创建一个 [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) 并在初始化 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) 时将其作为参数传递。

若要使用设备麦克风识别语音，只需创建 `SpeechRecognizer` 而无需传递 `AudioConfig`

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

> [!TIP]
> 如果要按 ID 引用设备，请使用 `AudioConfig(device_name="<device id>")` 创建 `AudioConfig`
> [了解如何获取音频输入设备的设备 ID](../../../how-to-select-audio-input-devices.md)。

如果要从音频文件（而不是使用麦克风）识别语音，请创建 [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) 并使用 `filename` 参数。

```Python
audio_input = speechsdk.AudioConfig(filename="your_file_name.wav")
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>识别语音

用于 Python 的语音 SDK 的[识别器类](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python)公开了一些可用于语音识别的方法。

* 单步识别（同步）- 在阻塞（同步）模式下执行识别。 在识别单个言语后返回。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。 该任务返回作为结果的识别文本。
* 单步识别（异步）- 在非阻塞（异步）模式下执行识别。 这将识别单个言语。 单个言语的结束是通过在结束时倾听静音或处理最长 15 秒音频时确定的。
* 连续识别（同步）- 同步启动连续识别。 客户端必须连接到 `EventSignal` 才能接收识别结果。 若要停止识别，请调用 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--)。
* 连续识别（异步）- 异步启动连续识别操作。 用户必须连接到 EventSignal 才能接收识别结果。 若要停止异步连续识别，请调用 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)。

> [!NOTE]
> 详细了解如何[选择语音识别模式](../../../how-to-choose-recognition-mode.md)。

### <a name="single-shot-recognition"></a>单步识别

下面是使用 [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once) 进行同步单步识别的示例：

```Python
result = speech_recognizer.recognize_once()
```

下面是使用 [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture) 进行异步单步识别的示例：

```Python
result = speech_recognizer.recognize_once_async()
```

无论你使用的是同步方法还是异步方法，都需要编写一些代码来循环访问结果。 此示例将计算 [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)：

* 输出识别结果：`speechsdk.ResultReason.RecognizedSpeech`
* 如果没有识别匹配项，请通知用户：`speechsdk.ResultReason.NoMatch `
* 如果遇到错误，则输出错误消息：`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>连续识别

连续识别涉及的方面比单步识别多一点。 它要求你连接到 `EventSignal` 以获取识别结果，必须调用 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) 或 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)。 下面是有关如何对音频输入文件执行连续识别的示例。

首先，我们将定义输入并初始化一个 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)：

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

接下来，让我们创建一个变量来管理语音识别的状态。 首先，我们将此设置为“`False`”，因为在开始识别时，我们可以放心地假定该操作尚未完成。

```Python
done = False
```

现在，我们将创建一个回叫，以在接收到 `evt` 时停止连续识别。 需谨记以下几点。

* 接收到 `evt` 时，系统将输出 `evt` 消息。
* 接收到 `evt` 后，系统将调用 [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) 来停止识别。
* 识别状态将更改为 `True`。

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

此代码示例演示如何将回叫连接到从 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--) 发送的事件。

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing)：事件信号，包含中间识别结果。
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized)：事件信号，包含最终识别结果（指示成功的识别尝试）。
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started)：事件信号，指示识别会话的开始（操作）。
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped)：事件信号，指示识别会话的结束（操作）。
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled)：事件信号，包含已取消的识别结果（指示因直接取消请求或者传输或协议失败导致的识别尝试取消）。

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

完成所有设置后，可以调用 [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped)。

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>听写模式

使用连续识别时，可以使用相应的“启用听写”功能启用听写处理。 此模式将导致语音配置实例解释句子结构的单词说明（如标点符号）。 例如，言语“你居住在城镇吗问号”会被解释为文本“你居住在城镇吗？”。

若要启用听写模式，请在 [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) 上使用 [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) 方法。

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>更改源语言

语音识别的常见任务是指定输入（或源）语言。 让我们看看如何将输入语言更改为德语。 在代码中找到 SpeechConfig，并直接在其下方添加此行。

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) 是采用字符串作为实参的形参。 可以提供受支持的[区域设置/语言](../../../language-support.md)的列表中的任何值。

## <a name="improve-recognition-accuracy"></a>提高识别准确度

可以通过多种方式使用语音 SDK 来提高识别的准确度。 让我们看一下短语列表。 短语列表用于标识音频数据中的已知短语，如人的姓名或特定位置。 可以将单个词或完整短语添加到短语列表。 在识别期间，如果音频中包含整个短语的完全匹配项，则使用短语列表中的条目。 如果找不到与短语完全匹配的项，则不支持识别。

> [!IMPORTANT]
> 短语列表功能仅以英语提供。

若要使用短语列表，请首先创建一个 [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) 对象，然后使用 [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-) 添加特定的单词和短语。

对 [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) 所做的任何更改都将在下一次识别或重新连接到语音服务之后生效。

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

如果需要清除短语列表： 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>提高识别精确度的其他方式

短语列表只是提高识别准确度的一种方式。 也可执行以下操作： 

* [使用自定义语音识别提高准确性](../../../how-to-custom-speech.md)
* [使用租户模型提高准确性](../../../tutorial-tenant-model.md)
