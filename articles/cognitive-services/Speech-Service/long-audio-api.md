---
title: 长音频 API （预览）-语音服务
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: b17b7088276aca6d7d8ed8f2d1cb554479b4de2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507634"
---
# <a name="long-audio-api-preview"></a>长音频 API （预览）

长音频 API 设计用于将长格式文本的异步合成到语音（例如：音频书籍）。 此 API 不会实时返回合成音频，而是期望你会轮询响应并使用这些输出，因为服务中提供了这些响应。 与语音 SDK 使用的文本到语音 API 不同，长音频 API 可以创建超过10分钟的合成音频，使其成为发布者和音频内容平台的理想选择。

长音频 API 的其他好处：

* 服务返回的合成语音使用神经声音，这可确保高保真音频输出。
* 由于实时响应不受支持，因此无需部署语音终结点。

## <a name="workflow"></a>工作流

通常，使用长音频 API 时，你将提交一个文本文件或文件以进行合成，轮询状态，如果状态为 "成功"，则可以下载音频输出。

此图提供工作流的高级概述。

![长音频 API 工作流关系图](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>为合成准备内容

准备文本文件时，请确保：

* 为纯文本（.txt）或 SSML 文本（.txt）
  * 对于纯文本，每个段落都按 "**输入/返回**-查看[纯文本输入" 示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)分隔
  * 对于 SSML 文本，每个 SSML 部分都被视为一个段落。 SSML 部分应由不同的段落分隔-查看[ssml 文本输入示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)。 有关语言代码，请参阅[语音合成标记语言（SSML）](speech-synthesis-markup.md)
* 编码为[utf-8，具有字节顺序标记（BOM）](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* 包含超过10000个字符或超过50个段落
* 是单个文件，而不是 zip

## <a name="audio-output-formats"></a>音频输出格式

长音频 API 支持以下音频输出格式：

> [!NOTE]
> 默认音频格式为 riff-16khz-16 位。

* riff-8khz-16 位
* riff-16khz-16 位
* riff-24khz-16 位
* riff-48khz-16 位
* 16khz-32kbitrate-mp3
* 16khz-64kbitrate-mp3
* 16khz-128kbitrate-mp3
* 24khz-48kbitrate-mp3
* 24khz-96kbitrate-mp3
* 24khz-160kbitrate-mp3

## <a name="quickstarts"></a>快速入门

我们提供旨在帮助你成功运行长音频 API 的快速入门。 此表包含按语言组织的长音频 API 快速入门列表。

* [快速入门： Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>代码示例
GitHub 上提供了长音频 API 的示例代码。

* [示例代码： Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [示例代码：C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [示例代码： Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="see-also"></a>另请参阅

* [长音频 API 参考](https://aka.ms/long-audio-ref)
