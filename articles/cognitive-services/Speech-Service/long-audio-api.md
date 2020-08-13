---
title: 长音频 API（预览）- 语音服务
titleSuffix: Azure Cognitive Services
description: 了解长音频 API 是如何为长格式文本转语音的异步合成而设计的。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: be38d3e78108a15c9f7875a15156e0eeba5a6211
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167753"
---
# <a name="long-audio-api-preview"></a>长音频 API（预览）

长音频 API 专为长格式文本转语音（例如有声读物、新闻文章和文档）的异步合成而设计。 此 API 不会实时返回合成音频，而是期望你轮询响应并消耗输出，因为服务中提供了这些响应和输出。 与语音 SDK 使用的文本转语音 API 不同，长音频 API 可以创建超过 10 分钟的合成音频，使其成为发布商和音频内容平台的的理想之选。

长音频 API 的其他好处：

* 服务返回的合成语音使用最佳的神经网络语音。
* 无需部署语音终结点，因为它可以在非实时批处理模式下合成语音。

> [!NOTE]
> 长音频 API 现在支持[公共神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)和[自定义神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)。

## <a name="workflow"></a>工作流

通常，使用长音频 API 时，需要提交要合成的文本文件并轮询状态，如果状态成功，则可以下载音频输出。

此图高度概括了该工作流。

![长音频 API 工作流关系图](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>为合成准备内容

准备文本文件时，请确保：

* 为纯文本 (.txt) 或 SSML 文本 (.txt)
* 编码为[包含字节顺序标记 (BOM) 的 UTF-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* 为单个文件，而不是 zip
* 包含 400 多个字符（对于纯文本），或包含 400 个[可计费字符](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)（对于 SSML 文本），并小于 10,000 个段落
  * 对于纯文本，通过点击 Enter/Return 来分隔每个段落 - 请查看[纯文本输入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * 对于 SSML 文本，每个 SSML 部分都被视为一个段落。 SSML 部分将分隔为不同的段落 - 请查看 [SSML 文本输入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 对于中文（大陆）、中文（香港特别行政区）、中文（台湾）、日语和韩语，一个字将计为两个字符。 

## <a name="python-example"></a>Python 示例

本部分包含的 Python 示例显示了长音频 API 的基本用法。 使用最喜欢的 IDE 或编辑器创建新的 Python 项目。 然后将以下代码片段复制到名为 `voice_synthesis_client.py` 的文件中。

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

这些库用于分析参数、构造 HTTP 请求，以及调用文本到语音转换长音频 REST API。

### <a name="get-a-list-of-supported-voices"></a>获取受支持语音列表

以下代码允许你获取可使用的特定区域/终结点的完整语音列表。 将此代码添加到 `voice_synthesis_client.py` 中：

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

使用命令运行脚本 `python voice_synthesis_client.py --voices -key <your_key> -region <region>` ，并替换以下值：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。

你将看到如下所示的输出：

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

如果 PublicVoice 参数为 True，则语音为公共神经语音。 否则，它是自定义的神经声音。

### <a name="convert-text-to-speech"></a>将文本转换为语音

在纯文本或 SSML 文本中准备输入文本文件，然后将以下代码添加到 `voice_synthesis_client.py` ：

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

使用命令运行脚本 `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` ，并替换以下值：

* 将 `<your_key>` 替换为语音服务订阅密钥。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<region>` 替换为创建语音资源的区域（例如：`eastus` 或 `westus`）。 [Azure 门户](https://aka.ms/azureportal)中资源的“概述”选项卡内提供了此信息。
* 将 `<input>` 替换为准备进行文本转语音的文本文件的路径。
* 将 `<locale>` 替换为所需的输出区域设置。 有关详细信息，请参阅[语言支持](language-support.md#neural-voices)。
* 将 `<voice_guid>` 替换为所需的输出语音。 使用之前对终结点的调用返回的其中一个声音 `/voicesynthesis/voices` 。

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

> [!NOTE]
> 如果有多个输入文件，则需要提交多个请求。 你需要注意以下限制。 
> * 对于每个 Azure 订阅帐户，客户端每秒最多可以向服务器提交 5 个请求。 如果超出限制，客户端将收到 429 错误代码（请求过多）。 请减少每秒请求数
> * 对于每个 Azure 订阅帐户，服务器最多可以运行 120 个请求并将其排入队列。 如果超出限制，服务器将返回 429 错误代码（请求过多）。 请耐心等待，避免在某些请求完成之前提交新请求

### <a name="remove-previous-requests"></a>删除以前的请求

服务将为每个 Azure 订阅帐户最多保留**20000**请求。 如果请求数量超出此限制，请在创建新请求之前删除以前的请求。 如果不删除现有请求，则会收到错误通知。

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

运行 `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` 以获取你所做的合成请求的列表。 你将看到如下所示的输出：

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

若要删除请求，请运行， `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` 并 `<synthesis_id>` 将替换为从上一个请求返回的请求 ID 值。

> [!NOTE]
> 无法移除或删除状态为“正在运行”/“正在等待”的请求。

`voice_synthesis_client.py` [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py)上提供了完整的。

## <a name="http-status-codes"></a>HTTP 状态代码

下表详细说明了 REST API 中的 HTTP 响应代码和消息。

| API | HTTP 状态代码 | 说明 | 解决方案 |
|-----|------------------|-------------|----------|
| 创建 | 400 | 此区域未启用语音合成。 | 使用受支持区域更改语音订阅密钥。 |
|        | 400 | 只有此区域的标准语音订阅才有效。 | 将语音订阅密钥更改为“标准”定价层。 |
|        | 400 | 超过 Azure 帐户的 20,000 个请求限制。 请在提交新请求之前删除一些请求。 | 服务器将为每个 Azure 帐户最多保留 20,000 个请求。 请在提交新请求之前删除一些请求。 |
|        | 400 | 此模型不能用于语音合成 {modelID}。 | 请确保 {modelID} 的状态正确。 |
|        | 400 | 请求区域与模型区域 {modelID} 不匹配。 | 请确保 {modelID} 区域与请求区域匹配。 |
|        | 400 | 语音合成仅支持使用包含字节顺序标记的 UTF-8 编码中的文本文件。 | 请确保输入文件使用包含字节顺序标记的 UTF-8 编码。 |
|        | 400 | 语音合成请求中只允许有效的 SSML 输入。 | 请确保输入 SSML 表达式正确。 |
|        | 400 | 在输入文件中找不到语音名称 {voiceName}。 | 输入 SSML 语音名称与模型 ID 不对齐。 |
|        | 400 | 输入文件中的段落数应小于 10,000。 | 请确保文件中的段落数小于 10,000。 |
|        | 400 | 输入文件应超过 400 个字符。 | 请确保输入文件超过 400 个字符。 |
|        | 404 | 找不到语音合成定义中声明的模型：{modelID}。 | 请确保 {modelID} 正确。 |
|        | 429 | 超出活动语音合成限制。 请等待直到一些请求完成。 | 对于每个 Azure 帐户，服务器最多可以运行 120 个请求并将其排入队列。 请等待并避免提交新请求，直到完成一些请求。 |
| 全部       | 429 | 请求太多。 | 对于每个 Azure 帐户，客户端每秒最多可以向服务器提交 5 个请求。 请减少每秒的请求数。 |
| Delete    | 400 | 语音合成任务仍在使用中。 | 只能删除“已完成”或“已失败”的请求 。 |
| GetByID   | 404 | 找不到指定的实体。 | 请确保合成 ID 正确。 |

## <a name="regions-and-endpoints"></a>区域和终结点

长音频 API 可用于具有单独终结点的多个区域。

| 区域 | 终结点 |
|--------|----------|
| 澳大利亚东部 | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| 加拿大中部 | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| 美国东部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| 印度中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 美国中南部 | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| 东南亚 | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西欧 | `https://westeurope.customvoice.api.speech.microsoft.com` |
| 美国西部 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>音频输出格式

我们支持灵活的语音输入格式。 可以通过设置“concatenateResult”参数，为每个段落生成音频输出或将音频输出串联到单个输出中。 长音频 API 支持以下音频输出格式：

> [!NOTE]
> 默认音频格式为 riff-16khz-16bit-mono-pcm。

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="sample-code"></a>代码示例
GitHub 上提供了长语音 API 的示例代码。

* [示例代码：Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [示例代码：C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [示例代码：Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
