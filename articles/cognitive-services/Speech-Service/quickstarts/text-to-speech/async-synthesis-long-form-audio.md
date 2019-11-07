---
title: 快速入门：用于长时间音频（预览）的异步合成-语音服务
titleSuffix: Azure Cognitive Services
description: 使用长音频 API 将文本异步转换为语音，并从服务提供的 URI 检索音频输出。 此 REST API 非常适合于需要将超过10000个字符或50段的文本文件转换为合成语音的内容提供商。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 2c1e6ef84a472ccec5116b12e18ad80c92b68960
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681881"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>快速入门： Python 中长格式音频的异步合成（预览版）

在本快速入门中，你将使用长音频 API 将文本异步转换为语音，并从服务提供的 URI 检索音频输出。 此 REST API 非常适合于需要将超过10000个字符或50段的文本文件转换为合成语音的内容提供商。 有关详细信息，请参阅[长音频 API](../../long-audio-api.md)。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* Python 2.7. x 或1.x。
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、 [Visual Studio Code](https://code.visualstudio.com/download)或你喜欢的文本编辑器。
* 一个 Azure 订阅和一个语音服务订阅密钥。 [创建一个 Azure 帐户](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account)，并[创建一个语音资源](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)以获取密钥。 创建语音资源时，请确保将定价层设置为**S0**，并将位置设置为[受支持的区域](../../regions.md#standard-and-neural-voices)。

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用最喜欢的 IDE 或编辑器创建新的 Python 项目。 然后将此代码片段复制到名为 `voice_synthesis_client.py`的文件中。

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

这些模块用于分析参数、构造 HTTP 请求，以及调用文本到语音转换长音频 REST API。

## <a name="get-a-list-of-supported-voices"></a>获取支持的语音的列表

此代码将获取可用于转换文本到语音转换的可用声音的列表。 添加此代码 `voice_synthesis_client.py`：

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

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

让我们测试到目前为止所做的工作。 运行此命令，将 `<your_key>` 替换为语音订阅密钥，并将其与创建语音资源的区域 `<region>` （例如： `eastus` 或 `westus`）。 此信息可在[Azure 门户](https://aka.ms/azureportal)中资源的 "**概述**" 选项卡中找到。

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

应会看到如下所示的输出：

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>将文本转换为语音

下一步是准备输入文本文件。 它可以是纯文本或 SSML，但不得超过10000个字符或50个段落。 有关要求的完整列表，请参阅[长音频 API](../../long-audio-api.md)。

准备好文本文件之后。 下一步是将用于语音合成的代码添加到项目。 将以下代码添加到 `voice_synthesis_client.py`：

> [!NOTE]
> 默认情况下，音频输出设置为 riff-16khz-16 位。 有关支持的音频输出的详细信息，请参阅[长音频 API](../../long-audio-api.md#audio-output-formats)。

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
        location = response.headers['Operation-Location']
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

让我们尝试使用输入文件作为源来发出合成文本请求。 需要在下面的请求中更新几项内容：

* 将 `<your_key>` 替换为语音服务订阅密钥。 此信息可在[Azure 门户](https://aka.ms/azureportal)中资源的 "**概述**" 选项卡中找到。
* 将 `<region>` 替换为在其中创建了语音资源的区域（例如： `eastus` 或 `westus`）。 此信息可在[Azure 门户](https://aka.ms/azureportal)中资源的 "**概述**" 选项卡中找到。
* 将 `<input>` 替换为要从文本到语音转换的文本文件的路径。
* 将 `<locale>` 替换为所需的输出区域设置。 有关详细信息，请参阅[语言支持](../../language-support.md#neural-voices)。
* 将 `<voice_guid>` 替换为音频输出的所需声音。 使用返回的其中一个语音[获取支持的声音列表](#get-a-list-of-supported-voices)，或使用[语言支持](../../language-support.md#neural-voices)中提供的神经声音列表。

用以下命令将文本转换为语音：

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> "concatenateResult" 是一个可选参数，如果未提供此参数，则输出将以多个波形文件的形式提供，每个文件对应一行。

应会看到如下所示的输出：

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

提供的结果包含服务生成的输入文本和音频输出文件。 这些将作为 zip 下载。

## <a name="remove-previous-requests"></a>删除以前的请求

每个订阅的请求数限制为2000。 因此，有时需要删除以前提交的请求，然后才能生成新的请求。 如果不删除现有请求，当超过2000时，会收到错误。

将以下代码添加到 `voice_synthesis_client.py`：

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

运行此命令，将 `<your_key>` 替换为语音订阅密钥，并将其与创建语音资源的区域 `<region>` （例如： `eastus` 或 `westus`）。 此信息可在[Azure 门户](https://aka.ms/azureportal)中资源的 "**概述**" 选项卡中找到。

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

这将返回你请求的 syntheses 的列表。 应会看到如下所示的输出：

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

现在，让我们使用其中一些值删除/删除以前提交的请求。 运行此命令，将 `<your_key>` 替换为语音订阅密钥，并将其与创建语音资源的区域 `<region>` （例如： `eastus` 或 `westus`）。 此信息可在[Azure 门户](https://aka.ms/azureportal)中资源的 "**概述**" 选项卡中找到。 `<synthesis_id>` 应是上一请求中返回的值之一。

> [!NOTE]
> 不能删除或删除状态为 "正在运行"/"正在等待" 的请求。

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

应会看到如下所示的输出：

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>获取完整客户端

可从[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)下载完整的 `voice_synthesis_client.py`。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关长音频 API 的详细信息](../../long-audio-api.md)
