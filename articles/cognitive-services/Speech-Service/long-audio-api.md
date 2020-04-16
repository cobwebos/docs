---
title: 长音频 API（预览） - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解长音频 API 如何设计为将长格式文本异步合成到语音。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401062"
---
# <a name="long-audio-api-preview"></a>长音频 API（预览版）

长音频 API 设计用于将长格式文本异步合成到语音（例如：有声读物）。 此 API 不会实时返回合成音频，而是期望您轮询响应并消耗从服务中提供的输出。 与语音 SDK 使用的文本到语音 API 不同，长音频 API 可以创建超过 10 分钟的合成音频，使其成为发布者和音频内容平台的理想选择。

长音频 API 的其他优势：

* 服务返回的合成语音使用神经语音，确保高保真音频输出。
* 由于不支持实时响应，因此无需部署语音终结点。

> [!NOTE]
> 长音频 API 现在仅支持[自定义神经语音](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)。

## <a name="workflow"></a>工作流

通常，使用长音频 API 时，您将提交要合成的文本文件或文件，轮询状态，然后，如果状态成功，则可以下载音频输出。

此图提供了工作流的高级概述。

![长音频 API 工作流图](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>准备供合成的内容

准备文本文件时，请确保：

* 是纯文本 （.txt） 或 SSML 文本 （.txt）
* 编码为[UTF-8，带字节订单标记 （BOM）](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* 是单个文件，而不是 zip
* 包含 400 多个字符的纯文本或 400 个[计费字符](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)的 SSML 文本，以及少于 10，000 个段落
  * 对于纯文本，每个段落通过点击 **"输入/返回**- 查看[纯文本输入"示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)进行分隔
  * 对于 SSML 文本，每个 SSML 部分都被视为段落。 SSML 部分应按不同的段落分隔 - 查看[SSML 文本输入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> 对于中文（大陆）、中文（香港）、中文（台湾）、日语和韩语，一个单词将计为两个字符。 

## <a name="submit-synthesis-requests"></a>提交综合请求

准备输入内容后，按照[长格式音频合成快速启动](https://aka.ms/long-audio-python)提交请求。 如果您有多个输入文件，则需要提交多个请求。 需要注意一些限制： 
* 允许客户端为每个 Azure 订阅帐户每秒向服务器提交最多 5 个请求。 如果超出限制，客户端将获得 429 错误代码（请求太多）。 请减少每秒的请求金额
* 允许服务器为每个 Azure 订阅帐户运行和排队多达 120 个请求。 如果超过限制，服务器将返回 429 错误代码（请求太多）。 请等待并避免提交新请求，直到某些请求完成
* 服务器将为每个 Azure 订阅帐户保留多达 20，000 个请求。 如果超过限制，请在提交新请求之前删除一些请求

## <a name="audio-output-formats"></a>音频输出格式

我们支持灵活的音频输出格式。 您可以通过设置"串联Result"参数，生成每个段落的音频输出或将音频串联到一个输出中。 长音频 API 支持以下音频输出格式：

> [!NOTE]
> 默认音频格式为 riff-16khz-16 位单声道 pcm。

* 里夫-8khz-16位单位pcm
* 里夫-16khz-16位单件
* riff-24khz-16 位单位 pcm
* riff-48khz-16位单位pcm
* 音频-16khz-32kbitrate-单声道mp3
* 音频-16khz-64kbitrate-单声道mp3
* 音频-16khz-128kbitrate-单声道mp3
* 音频-24khz-48kbitrate-单声道mp3
* 音频-24khz-96kbitrate-单声道mp3
* 音频-24khz-160kbitrate-单声道mp3

## <a name="quickstarts"></a>快速入门

我们提供快速入门，旨在帮助您成功运行长音频 API。 此表包括按语言组织的长音频 API 快速启动的列表。

* [快速入门：Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>示例代码
GitHub 上提供了长音频 API 的示例代码。

* [示例代码：Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [示例代码： C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [示例代码：Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
