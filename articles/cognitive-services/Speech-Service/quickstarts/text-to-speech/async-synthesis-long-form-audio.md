---
title: 快速入门：用于长格式音频的异步合成（预览版）- 语音服务
titleSuffix: Azure Cognitive Services
description: 使用长音频 API 将文本异步转换为语音，并从服务提供的 URI 中检索音频输出。 对于需要将超过 10,000 个字符或 50 个段落的文本文件转换为合成语音的内容提供程序，此 REST API 是理想之选。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.custom: devx-track-python
ms.openlocfilehash: 330bd3593ea10e4a4a03e430025b8acf1f7ab21a
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875381"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>快速入门：Python 中用于长格式音频的异步合成（预览版）

在本快速入门中，你将使用长音频 API 将文本异步转换为语音，并从服务提供的 URI 中检索音频输出。 对于需要从超过 5,000 个字符（或长度超过 10 分钟）的文本中合成音频的内容提供程序，此 REST API 是理想之选。 有关详细信息，请参阅[长音频 API](../../long-audio-api.md)。

用于长[音频的异步合成音频和](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)[自定义的神经声音](../../how-to-custom-voice.md#custom-neural-voices)，其中每个都支持特定语言和方言。 

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* Python 2.7.x 或 3.x。
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器。
* Azure 订阅和语音服务订阅密钥。 [创建 Azure 帐户](../../get-started.md#new-resource)并[创建语音资源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource)来获取密钥。 创建语音资源时，请确保将定价层设置为 S0，将位置设置为[支持的区域](../../regions.md#standard-and-neural-voices)。

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用最喜欢的 IDE 或编辑器创建新的 Python 项目。 然后将以下代码片段复制到名为 `voice_synthesis_client.py` 的文件中。

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `pip install requests urllib3`。

这些模块用于分析参数、构造 HTTP 请求以及调用文本转语音长音频 REST API。

## <a name="get-a-list-of-supported-voices"></a>获取受支持语音列表

以下代码允许你获取可使用的特定区域/终结点的完整语音列表。 请检查[支持的区域/终结点](../../long-audio-api.md)。 将此代码添加到 `voice_synthesis_client.py` 中：

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>测试代码

让我们测试一下到目前为止你的工作成效。 你需要在以下请求中更新几项内容：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。

运行以下命令：

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

你将看到如下所示的输出：

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

如果 PublicVoice 参数为 True，则语音为公共神经语音。 否则，它是自定义的神经声音。 

## <a name="prepare-input-files"></a>准备输入文件

准备输入文本文件。 它可以是纯文本或 SSML 文本。 有关输入文件的要求，请参阅如何[准备要合成的内容](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis)。

## <a name="convert-text-to-speech"></a>将文本转换为语音

准备好输入文本文件后，将下面用于语音合成的代码添加到 `voice_synthesis_client.py` 中：

> [!NOTE]
> “concatenateResult”是一个可选参数。 如果未设置此参数，则将按段落生成音频输出。 你还可以通过设置该参数，将音频连接成 1 个输出。 默认情况下，音频输出设置为 riff-16khz-16bit-mono-pcm。 有关支持的音频输出的详细信息，请参阅[音频输出格式](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)。

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>测试代码

让我们使用输入文件作为源来发出合成文本请求。 你需要在以下请求中更新几项内容：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<input>` 替换为准备进行文本转语音的文本文件的路径。
* 将 `<locale>` 替换为所需的输出区域设置。 有关详细信息，请参阅[语言支持](../../language-support.md#neural-voices)。
* 将 `<voice_guid>` 替换为所需的输出语音。 使用[获取受支持语音列表](#get-a-list-of-supported-voices)返回的语音之一。

使用以下命令将文本转换为语音：

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> 如果有多个输入文件，则需要提交多个请求。 你需要注意以下限制。 
> * 对于每个 Azure 订阅帐户，客户端每秒最多可以向服务器提交 5 个请求。 如果超出限制，客户端将收到 429 错误代码（请求过多）。 请减少每秒请求数
> * 对于每个 Azure 订阅帐户，服务器最多可以运行 120 个请求并将其排入队列。 如果超出限制，服务器将返回 429 错误代码（请求过多）。 请耐心等待，避免在某些请求完成之前提交新请求

你将看到如下所示的输出：

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

结果包含服务生成的输入文本和音频输出文件。 可以通过 zip 的形式下载这些文件。

## <a name="remove-previous-requests"></a>删除以前的请求

服务器最多为每个 Azure 订阅帐户保留 20,000 个请求。 如果请求数量超出此限制，请在创建新请求之前删除以前的请求。 如果不删除现有请求，则会收到错误通知。

将此代码添加到 `voice_synthesis_client.py` 中：

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>测试代码

现在，让我们检查一下你以前提交的请求。 在继续下一步操作前，你需要在以下请求中更新几项内容：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。

运行以下命令：

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

此命令将返回你发出的合成请求的列表。 你将看到如下所示的输出：

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

现在，让我们删除以前提交的请求。 你需要在以下代码中更新几项内容：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<synthesis_id>` 替换为上一个请求中返回的值。

> [!NOTE]
> 无法移除或删除状态为“正在运行”/“正在等待”的请求。

运行以下命令：

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

你将看到如下所示的输出：

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>获取完整客户端

可在 [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) 上下载完整的 `voice_synthesis_client.py`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解长音频 API](../../long-audio-api.md)
