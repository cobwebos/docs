---
title: 什么是语音服务（预览）？ | Microsoft Docs
description: 语音服务属于 Microsoft 认知服务，其中合并了多个以前单独提供的 Azure 语音服务：必应语音（包括语音识别和文本转语音）、自定义语音和语音翻译。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e7c09eee1634c52e78a523a7cc65641ea99f23e6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "35366970"
---
# <a name="what-is-the-speech-service-preview"></a>什么是语音服务（预览）？

语音服务属于 Microsoft 认知服务，其中合并了多个以前单独提供的 Azure 语音服务：必应语音（包括语音识别和文本转语音）、自定义语音和语音翻译。 与前身类似，语音服务由其他 Microsoft 产品（包括 Cortana 和 Microsoft Office）中使用的技术提供支持。

> [!NOTE]
> 语音服务目前提供公共预览版。 请定期返回此处获取文档更新、更多示例代码等内容。

借助一个订阅，开发人员即可利用一体化语音服务轻松为应用程序增加强大的语音支持功能。 应用现可支持语音命令、听录、听写、语音合成和翻译。

|函数|说明|
|-|-|
|语音转文本|将连续的人类语音转换为可用作应用程序输入的文本。 可与[语言理解服务](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) 集成，用于从陈述派生用户意向。|
|文本到语音转换|将文本转换为发音自然的合成语音音频文件。|
|语音&nbsp;翻译|支持将语音翻译成其他语言，输出为文本或语音。|

## <a name="using-the-speech-service"></a>使用语音服务

可通过两种方式使用语音服务。 [SDK](speech-sdk.md) 可提取网络协议的详细信息。 [REST API](rest-apis.md) 适用于任何编程语言，但不提供 SDK 提供的所有功能。

|<br>方法|语音<br>转文本|文本转<br>语音|语音<br>翻译|<br>说明|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|是|否|是|特定编程语言的库，可简化开发。|
|[REST](rest-apis.md)|是|是|否|一个基于 HTTP 的简单 API，可用于轻松将语音添加到应用程序。|

## <a name="speech-to-text"></a>语音转文本

[语音转文本](speech-to-text.md) (STT)（或语音识别）API 可将音频流转录成作为应用程序的可接受输入的文本。 然后，应用程序可以（例如）将文本输入文档，或作为命令对其执行操作。

语音转文本已针对交互式对话和听写场景经过单独优化。 以下是语音转文本 API 的常见用例。 

* 识别简要陈述，例如命令，但不提供中间结果
* 听录以前录制的一段较长陈述，如语音信箱消息
* 实时听录流式处理语音以进行听写，提供部分结果
* 根据所说的自然语言请求确定用户希望执行的操作

语音转文本 API 支持交互式语音听录，可进行实时连续识别并提供中间结果。 它还支持语音结束检测、可选自动大写和标点、不雅内容屏蔽和文本规范化。

可以自定义语音转文本声学和语言模型，使之适应专用词汇、杂音环境和不同的说话方式。

## <a name="text-to-speech"></a>文本到语音转换

[文本转语音](text-to-speech.md) (TTS)（或语音合成）API 可将纯文本转换为发音自然的语音，将音频文件发送到应用程序。 我们为多种支持语言提供了多种语音（不同性别或口音）。

该 API 支持语音合成标记语言 (SSML) 标记，因此，可为令人烦恼的字词指定确切的语音发音。 SSML 还可以直接在文本中指示语音特征（包括强调、速度、音量、性别和音高）。

以下是文本转语音 API 的常见用例。

* 作为备用屏幕输出提供给有视觉障碍的用户的语音输出
* 用于车内应用程序（如导航）的语音提示
* 与语音转文本 API 协作的对话用户界面

文本转语音 API 支持自定义语音模型，适用于需要不受支持的方言或只需要适用于应用程序的唯一声音的情况。

## <a name="speech-translation"></a>语音翻译

可使用 [语音翻译](speech-translation.md) API 几乎实时翻译流式处理音频或者处理录音。 在流式处理翻译中，该服务将返回可向用户显示用于指示翻译进度的中间结果。 结果可能会作为文本或语音返回。

语音翻译的用例如下。

* 为旅行者实现“对话”翻译移动应用或设备 
* 为录制的音频和视频的字幕提供自动翻译

## <a name="speech-devices-sdk"></a>语音设备 SDK

随着一体化语音服务的推出，Microsoft 及其合作伙伴为开发语音支持设备提供了优化的集成硬件/软件平台：[语音设备 SDK](speech-devices-sdk.md)。 此 SDK 适用于面向所有类型的应用程序开发智能语音设备。

语音设备 SDK 支持使用自定义唤醒字词构建自己的环境设备，这样，触发音频捕获的提示词将为品牌特有。 它还通过多声道音频源提供高级音频处理（包括噪声抑制、远场语音和波束赋形），以提供语音识别的准确性。

## <a name="next-steps"></a>后续步骤

获取语音服务的免费试用订阅密钥。

> [!div class="nextstepaction"]
> [免费试用语音服务](get-started.md)
