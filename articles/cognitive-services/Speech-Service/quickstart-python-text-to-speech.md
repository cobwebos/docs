---
title: 快速入门：将文本转换为语音 (Python) - 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用 Python 和文本转语音 REST API 将文本转换为语音。 本指南中包含的示例文本以语音合成标记语言 (SSML) 的形式构造。 这样你就可以选择语音响应的语音和语言。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/16/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7512c443c49f1518d6a6f7ace7e981d3508b1d7a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090051"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>快速入门：使用 Python 将文本转换为语音

本快速入门介绍如何使用 Python 和文本转语音 REST API 将文本转换为语音。 本指南中包含的请求正文以[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 的形式构造，这样你就可以选择响应的语音和语言。

此快速入门需要包含语音服务资源的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](get-started.md)获取订阅密钥。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* Python 2.7.x 或 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器
* 语音服务的 Azure 订阅密钥

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用最喜欢的 IDE 或编辑器创建新的 Python 项目。 然后，将此代码片段复制到项目的名为 `tts.py` 的文件中。

```python
import os, requests, time
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `pip install requests`。

这些模块用于在带时间戳的文件中写入语音响应、构造 HTTP 请求，以及调用文本转语音 API。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>设置订阅密钥并为 TTS 创建提示

在以下几个部分，我们将创建一些方法用于处理授权、调用文本转语音 API，以及验证响应。 首先让我们添加一些代码，以确保此示例适用于 Python 2.7.x 和 3.x。

```python
try: input = raw_input
except NameError: pass
```

接下来，让我们创建一个类。 我们将在此类中放置用于交换令牌和调用文本转语音 API 的方法。

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` 是 Azure 门户中的唯一密钥。 `tts` 提示用户输入要转换为语音的文本。 此输入是字符串文本，因此无需将字符转义。 最后，`timestr` 获取当前时间，我们将使用此时间来为文件命名。

## <a name="get-an-access-token"></a>获取访问令牌

文本转语音 REST API 需要使用访问令牌进行身份验证。 若要获取访问令牌，需要进行交换。 此示例通过 `issueToken` 终结点使用语音服务订阅密钥来交换访问令牌。

此示例假定语音服务订阅位于“美国西部”区域。 如果使用其他区域，请更新 `fetch_token_url` 的值。 如需完整的列表，请参阅[区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

将以下代码复制到 `TextToSpeech` 类中：

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> 有关身份验证的详细信息，请参阅[如何获取访问令牌](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#how-to-get-an-access-token)。

## <a name="make-a-request-and-save-the-response"></a>发出请求并保存响应

此处我们将要生成请求并保存语音响应。 首先需要设置 `base_url` 和 `path`。 此示例假设使用“美国西部”终结点。 如果已将资源注册到其他区域，请务必更新 `base_url`。 有关详细信息，请参阅[语音服务区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下来，需要为请求添加所需的标头。 请务必使用资源的名称（在 Azure 门户中可以找到）更新 `User-Agent`，并将 `X-Microsoft-OutputFormat` 设置为首选的音频输出。 如需输出格式的完整列表，请参阅[音频输出](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs)。

然后，使用语音合成标记语言 (SSML) 构造请求正文。 此示例将定义结构，并使用前面创建的 `tts` 输入。

>[!NOTE]
> 此示例使用 `ZiraRUS` 语音字体。 如需 Microsoft 提供的语音/语言的完整列表，请参阅[语言支持](language-support.md)。
> 若要为品牌创建唯一且可识别的语音，请参阅[创建自定义语音字体](how-to-customize-voice-font.md)。

最后，向服务发出请求。 如果该请求成功，则会返回 200 状态代码，语音响应将写入到带时间戳的文件。

将以下代码复制到 `TextToSpeech` 类中：

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME',
        'cache-control': 'no-cache'
    }
    body = "<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" + self.tts + "</voice></speak>"

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")

```

## <a name="put-it-all-together"></a>将其放在一起

即将完成。 最后一步是实例化上述类并调用函数。

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，可以运行文本转语音示例应用。 从命令行（或终端会话）导航到项目目录，然后运行以下命令：

```console
python tts.py
```

出现提示时，键入要转换为语音的任何文本内容。 如果转换成功，则项目文件夹中会出现语音文件。 使用偏好的媒体播放器播放该文件。

## <a name="clean-up-resources"></a>清理资源

请务必删除示例应用的源代码中的机密信息，例如订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>另请参阅

* [创建自定义语音字体](how-to-customize-voice-font.md)
* [录制语音样本用于创建自定义语音](record-custom-voice-samples.md)
