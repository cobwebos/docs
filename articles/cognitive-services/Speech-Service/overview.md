---
title: 什么是语音服务（预览）？
description: 语音服务属于 Microsoft 认知服务，其中合并了多个以前单独提供的 Azure 语音服务：必应语音（包括语音识别和文本转语音）、自定义语音和语音翻译。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445628"
---
# <a name="what-is-the-speech-service-preview"></a>什么是语音服务（预览）？

语音服务由其他 Microsoft 产品（包括 Cortana 和 Microsoft Office）中使用的技术提供支持。  任何客户都可使用相同服务作为认知服务。 

> [!NOTE]
> 语音服务目前提供公共预览版。 请定期返回此处获取文档更新、更多示例代码等内容。

借助一个订阅，开发人员即可利用语音服务轻松为应用程序增加强大的语音支持功能。 应用现可支持语音命令、听录、听写、语音合成和翻译。

## <a name="speech-service-features"></a>语音服务功能

|函数|Description|
|-|-|
|[语音转文本](speech-to-text.md)| 将音频流转录为应用程序可以接受为输入的文本。 可与[语言理解服务](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) 集成，用于从话语派生用户意向。|
|[文本转语音](text-to-speech.md)| 将纯文本转换为发音自然的语音，将音频文件发送到应用程序。 我们为多种支持语言提供了多种语音（不同性别或口音）。 |
|[语音翻译](speech-translation.md)| 可用于几乎实时翻译流式音频或者处理录音。 |
|[语音设备 SDK](speech-devices-sdk.md)| 随着一体化语音服务的推出，Microsoft 及其合作伙伴为开发语音支持设备提供了优化的集成硬件/软件平台 |

## <a name="using-the-speech-service"></a>使用语音服务

可通过两种方式使用语音服务。 [SDK](speech-sdk.md) 可提取网络协议的详细信息。 [REST API](rest-apis.md) 适用于任何编程语言，但不提供 SDK 提供的所有功能。

|<br>方法|语音<br>转文本|文本转<br>语音|语音<br>翻译|<br>Description|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|是|否|是|特定编程语言的库，可简化开发。|
|[REST](rest-apis.md)|是|是|否|一个基于 HTTP 的简单 API，可用于轻松将语音添加到应用程序。|

## <a name="next-steps"></a>后续步骤

获取语音服务的免费试用订阅密钥。

> [!div class="nextstepaction"]
> [免费试用语音服务](get-started.md)
