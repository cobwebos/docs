---
title: 关于语音 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音软件开发工具包 （SDK） 公开了许多语音服务功能，从而更轻松地开发启用语音的应用程序。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 440944033e4b6a8fb29cf9ace6ad784b91316526
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668765"
---
# <a name="about-the-speech-sdk"></a>关于语音 SDK

语音软件开发工具包 （SDK） 公开了许多语音服务功能，使您能够开发支持语音的应用程序。 语音 SDK 提供许多编程语言和所有平台。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>方案功能

语音 SDK 公开了语音服务中的许多功能，但不是全部功能。 语音 SDK 的功能通常与方案相关联。 语音 SDK 非常适合使用本地设备、文件、Azure Blob 存储，甚至输入和输出流的实时和非实时方案。 当语音 SDK 无法实现方案时，请查找 REST API 替代方案。

### <a name="speech-to-text"></a>语音转文本

[语音到文本](speech-to-text.md)（也称为*语音识别*）将音频流转录到应用程序、工具或设备可以使用或显示的文本。 结合[语言理解 (LUIS)](../luis/index.yml) 使用语音转文本可以从听录的语音中派生用户意向，以及处理语音命令。 使用[语音翻译](speech-translation.md)将语音输入转换为具有单个调用的不同语言。 有关详细信息，请参阅[语音到文本基础知识](speech-to-text-basics.md)。

### <a name="text-to-speech"></a>文本转语音

[文本到语音](text-to-speech.md)（也称为*语音合成*）将文本转换为类似人类的合成语音。 输入文本是字符串文本或使用[语音合成标记语言 （SSML）。](speech-synthesis-markup.md) 有关标准语音或神经语音的详细信息，请参阅[文本到语音语言和语音支持](language-support.md#text-to-speech)。

### <a name="voice-assistants"></a>语音助手

使用语音 SDK 的语音助理使开发人员能够为其应用程序和体验创建自然的、类似于人的对话接口。 语音助理服务提供设备和助手之间快速、可靠的交互。 实现使用机器人框架的直线语音通道或集成的自定义命令（预览）服务来完成任务。 此外，可以使用[自定义语音门户](https://aka.ms/customvoice)创建语音助理，以创建独特的语音体验。

#### <a name="keyword-spotting"></a>关键字发现

语音 SDK 中支持[关键字发现](speech-devices-sdk-create-kws.md)的概念。 关键字识别是识别语音中关键字的行为，随后在听到关键字时执行操作。 例如，"嘿，Cortana"将激活 Cortana 助手。

### <a name="meeting-scenarios"></a>会议方案

语音 SDK 非常适合从单个设备或多设备对话转录会议方案。

#### <a name="conversation-transcription"></a>对话听录

[对话转录](conversation-transcription.md)支持对每个演讲者的实时（和异步）语音识别、扬声器识别和句子归属（也称为*二分化*）。 它非常适合用于听录能够区分说话人的面对面会谈场景。

#### <a name="multi-device-conversation"></a>多设备对话

使用[多设备对话](multi-device-conversation.md)，在对话中连接多个设备或客户端，发送基于语音或基于文本的消息，轻松支持转录和翻译。

### <a name="custom--agent-scenarios"></a>自定义/代理方案

语音 SDK 可用于转录呼叫中心方案，其中生成电话数据。

#### <a name="call-center-transcription"></a>呼叫中心听录

[呼叫中心转录](call-center-transcription.md)是语音到文本的常见方案，用于转录可能来自各种系统（如交互式语音响应 （IVR））的大量电话数据。 语音服务的最新语音识别模型擅长转录此电话数据，即使在数据难以理解的情况下也是如此。

### <a name="codec-compressed-audio-input"></a>编解码器压缩音频输入

一些语音 SDK 编程语言支持编解码器压缩音频输入流。 有关详细信息，请参阅<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">使用压缩音频输入格式<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="rest-api"></a>REST API

虽然语音 SDK 涵盖语音服务的许多功能功能，但对于某些方案，您可能需要使用 REST API。

### <a name="batch-transcription"></a>批量听录

[批处理转录](batch-transcription.md)支持对大量数据的异步语音转录。 批处理转录只能从 REST API 进行。 除了将语音音频转换为文本外，批量语音到文本还允许分二化和情绪分析。

## <a name="customization"></a>自定义

语音服务具有跨语音到文本、文本到语音转换和语音转换的默认模型，提供了出色的功能。 有时，您可能希望提高基准性能，以更好地使用独特的用例。 语音服务具有各种无代码自定义工具，使之变得简单，并允许您利用基于您自己的数据的自定义模型创建竞争优势。 这些模型将仅对您和您的组织可用。

### <a name="custom-speech-to-text"></a>自定义语音转文本

在独特的环境中使用语音到文本进行识别和转录时，您可以创建并训练自定义声学、语言和发音模型，以解决环境噪音或行业特定的词汇。 创建和管理无代码自定义语音模型可通过[自定义语音门户](https://aka.ms/customspeech)获得。 发布自定义语音模型后，语音 SDK 就可以使用它。

### <a name="custom-text-to-speech"></a>自定义文本到语音

自定义文本到语音，也称为自定义语音是一组在线工具，允许您为您的品牌创建可识别的、一种类型的声音。 创建和管理无代码自定义语音模型可通过[自定义语音门户](https://aka.ms/customvoice)获得。 发布自定义语音模型后，语音 SDK 就可以使用它。

## <a name="get-the-speech-sdk"></a>获取语音 SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[浏览器](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
