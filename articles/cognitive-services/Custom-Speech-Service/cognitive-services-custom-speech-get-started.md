---
title: Azure 上的自定义语音服务入门 | Microsoft Docs
description: 订阅自定义语音服务，并将服务活动与 Azure 订阅相关联，以定型模型和执行部署。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fe7a140f5ba2d712014f03663a88d516958d188e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365515"
---
# <a name="get-started-with-custom-speech-service"></a>自定义语音服务入门

探索自定义语音服务的主要功能，并了解如何生成、部署和使用声学模型和语言模型来满足应用需求。 在自定义语音服务门户上注册后，可以查看更丰富的文档和分步说明。

## <a name="samples"></a>示例  
我们提供了实用的参考示例，可单击[此处](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)获取。

## <a name="prerequisites"></a>先决条件  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>订阅自定义语音服务并获取订阅密钥
必须订阅自定义语音服务并获取订阅密钥（请参阅[订阅](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech)或按照[此处](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md)的说明操作），才能使用上述示例。 主密钥和辅助密钥都可用于本教程。 为了确保 API 密钥的机密性和安全性，请务必遵循最佳做法。

### <a name="get-the-client-library-and-example"></a>获取客户端库和示例
可通过 [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk) 下载客户端库和示例。 必须将下载的 zip 文件解压缩到选定文件夹，许多用户选择解压缩到 Visual Studio 2015 文件夹。

## <a name="creating-a-custom-acoustic-model"></a>创建自定义声学模型
若要向特定域自定义声学模型，必须有一系列语音数据。 此集合包括一组语音数据音频文件，以及每个音频文件的听录文本文件。 音频数据应能够代表要使用识别器的场景

例如：若要更好地识别嘈杂工厂环境中的语音，音频文件应包括嘈杂工厂中的说话人。
若要优化一个说话人的讲话（例如，要听录罗斯福总统的所有炉边谈话），音频文件应只包括这个说话人的多个示例。

可以单击[此处](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md)，详细了解如何创建自定义声学模型。

## <a name="creating-a-custom-language-model"></a>创建自定义语言模型
自定义语言模型和声学模型的创建过程类似，区别在于前者没有音频数据，只包含文本。 文本应包含用户预期说出或已记录用户在应用中说出（或键入）的许多查询或陈述示例。

可以单击[此处](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md)，详细了解如何创建自定义语言模型。

## <a name="creating-a-custom-speech-to-text-endpoint"></a>创建自定义语音转文本终结点
创建自定义声学模型和/或语言模型后，可以在自定义语音转文本终结点中部署它们。 若要新建自定义终结点，请单击页面顶部“CRIS”菜单中的“部署”。 这会重定向到当前自定义终结点的“部署”表。 如果尚未创建任何终结点，此表为空。 表标题反映了当前的区域设置。 若要为其他语言创建部署，请选择“更改区域设置”。 若要详细了解支持的语言，请参阅“更改区域设置”部分。

可以单击[此处](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md)，详细了解如何创建自定义语音转文本终结点。

## <a name="using-a-custom-speech-endpoint"></a>使用自定义语音终结点
向 CRIS 语音转文本终结点和 Microsoft 认知服务语音终结点发送请求的方式非常类似。 请注意，这些终结点的功能与语音 API 的默认终结点完全相同。 因此，通过语音 API 的客户端库或 REST API 可使用的功能也同样适用于自定义终结点。

可以单击[此处](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md)，详细了解如何使用自定义语音转文本终结点。


请注意，通过 CRIS 创建的终结点可以处理的并发请求数因订阅层而异。 如果识别数多于请求数，就会返回错误代码 429（请求次数过多）。 有关详细信息，请访问[定价信息](https://www.microsoft.com/cognitive-services/en-us/pricing)。 此外，免费层还有每月请求数配额。 如果在免费层中访问终结点的次数超过每月配额，服务会返回错误代码 403“已禁止”。

服务假定音频为实时传输。 如果发送速度较快，请求就会被视为正在运行，直到实时传输结束。

* [概述](cognitive-services-custom-speech-home.md)
* [常见问题](cognitive-services-custom-speech-faq.md)
* [术语表](cognitive-services-custom-speech-glossary.md)
