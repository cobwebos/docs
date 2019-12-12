---
title: 快速入门：列出“文本转语音”语音，Python - 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用 Python 获取某个区域/终结点的标准语音和神经语音的完整列表。 列表以 JSON 形式返回，语音可用性因区域而异。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: c645d01dd37fd370911019446c99c76c9da6221c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975969"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>快速入门：使用 Python 获取“文本转语音”语音的列表

本快速入门介绍如何使用 Python 获取某个区域/终结点的标准语音和神经语音的完整列表。 列表以 JSON 形式返回，语音可用性因区域而异。 有关受支持的区域的列表，请参阅[区域](regions.md)。

此快速入门需要包含语音服务资源的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](get-started.md)获取订阅密钥。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* Python 2.7.x 或 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器
* 语音服务的 Azure 订阅密钥

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用最喜欢的 IDE 或编辑器创建新的 Python 项目。 然后，将此代码片段复制到项目的名为 `get-voices.py` 的文件中。

```python
import requests
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `pip install requests`。

这些请求用于对“文本转语音”服务的 HTTP 请求。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>设置订阅密钥并为 TTS 创建提示

在以下几个部分，我们将创建一些方法用于处理授权、调用文本转语音 API，以及验证响应。 首先，让我们创建类。 我们将在此类中放置用于交换令牌和调用文本转语音 API 的方法。

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` 是 Azure 门户中的唯一密钥。

## <a name="get-an-access-token"></a>获取访问令牌

此终结点需要使用访问令牌进行身份验证。 若要获取访问令牌，需要进行交换。 此示例通过 `issueToken` 终结点使用语音服务订阅密钥来交换访问令牌。

此示例假定语音服务订阅位于“美国西部”区域。 如果使用其他区域，请更新 `fetch_token_url` 的值。 如需完整的列表，请参阅[区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

将以下代码复制到 `GetVoices` 类中：

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
> 有关身份验证的详细信息，请参阅[使用访问令牌进行身份验证](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

## <a name="make-a-request-and-save-the-response"></a>发出请求并保存响应

此处我们将要生成请求并保存返回的语音列表。 首先需要设置 `base_url` 和 `path`。 此示例假设使用“美国西部”终结点。 如果已将资源注册到其他区域，请务必更新 `base_url`。 有关详细信息，请参阅[语音服务区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下来，为请求添加所需的标头。 最后，向服务发出请求。 如果请求成功，并返回 200 状态代码，则响应将写入文件。

将以下代码复制到 `GetVoices` 类中：

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>将其放在一起

即将完成。 最后一步是实例化上述类并调用函数。

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，就可以运行示例了。 从命令行（或终端会话）导航到项目目录，然后运行以下命令：

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>清理资源

请务必删除示例应用的源代码中的机密信息，例如订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Python 示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>另请参阅

* [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [创建自定义语音字体](how-to-customize-voice-font.md)
* [录制语音样本用于创建自定义语音](record-custom-voice-samples.md)
