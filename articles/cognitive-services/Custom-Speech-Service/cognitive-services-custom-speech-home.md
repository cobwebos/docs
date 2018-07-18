---
title: Azure 上的自定义语音服务概述 | Microsoft Docs
description: 自定义语音服务是一种基于云的服务，借助该服务，可自定义语音到文本转换的语音模型。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ms.openlocfilehash: a6139283a555f8f97371c02f9d1f3d53dc6f15d3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365901"
---
# <a name="what-is-custom-speech-service"></a>什么是自定义语音服务？

自定义语音服务是一种基于云的服务，让用户能自定义语音到文本转换的语音模型。
若要使用自定义语音服务，请参阅[自定义语音服务门户](https://cris.ai)。

通过自定义语音服务，可为应用程序和用户创建精确符合其需要的自定义语言模型和声学模型。 将特定语音和/或文本数据上传到自定义语音服务后，可创建自定义模型，该模型可与 Microsoft 目前最先进的语音模型结合使用。

例如，如果要向移动电话、平板电脑或 PC 应用添加语音交互功能，可创建能与 Microsoft 声学模型结合使用的自定义语言模型，从而创建专为特定应用而设计的语音到文本的终结点。 如果应用程序针对特定环境或特定用户群而设，还可使用此服务创建和部署自定义声学模型。


## <a name="how-do-speech-recognition-systems-work"></a>语音识别系统的工作原理
语音识别系统由几个协同工作的组件构成。 其中最重要的两个组件是声学模型和语言模型。

在给定语言中，声学模型是一个分类器，可将短小声音片段标识为若干不同音素或声音单位中的一种。 例如，“speech”由四个音素“s p iy ch”组成。 每秒大约会进行 100 次这样的分类。

语言模型是词语序列的概率分布。 语言模型可帮助系统根据词语序列自身发生的可能性，从发音类似的词语序列中做出选择。 例如，“recognize speech”和“wreck a nice beach”发音类似，但前者的可能性要高得多，因此语言模型会给“recognize speech”分配更高的分值。

声学模型和语言模型都是通过数据训练得到的统计模型。 因此，在应用程序中使用这些模型时，如果模型遇到和处理的语音与训练期间观察的数据类似，则模型表现最佳。 Microsoft 语音转文本引擎中的声学模型和语言模型已接受了大量语音和文本集的训练，在最常见的使用场景中性能极为出色，例如与智能手机、平板电脑或 PC 上的 Cortana 交互，通过语音搜索 Web 或通过语音向朋友发送短信。

## <a name="why-use-the-custom-speech-service"></a>为什么要使用自定义语音服务？
Microsoft 语音转文本引擎是一流的引擎，专用于上述各种场景。 但如果希望应用程序的语音查询包含特定词汇项（例如日常语言中少用的产品名称或专用术语），那么使用自定义语言模型很可能获得更好的效果。

例如，假如要构建通过语音搜索 MSDN 的应用，那么相较于一般语音应用程序，该应用用到“面向对象”、“命名空间”或“.Net”等术语的几率则很可能提升。 自定义语言模型能让系统掌握这种差异。

## <a name="next-steps"></a>后续步骤

有关如何使用自定义语音服务的详细信息，请参阅 [自定义语音服务门户] (https://cris.ai))。

* [入门](cognitive-services-custom-speech-get-started.md)
* [常见问题](cognitive-services-custom-speech-faq.md)
* [术语表](cognitive-services-custom-speech-glossary.md)
