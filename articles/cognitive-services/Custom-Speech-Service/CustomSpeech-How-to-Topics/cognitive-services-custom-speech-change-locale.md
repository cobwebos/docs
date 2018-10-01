---
title: 支持的区域设置和语言 - 自定义语音服务
titlesuffix: Azure Cognitive Services
description: 认知服务中自定义语音服务的支持语言的概述。
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223949"
---
# <a name="supported-locales-in-custom-speech-service"></a>自定义语音服务中支持的区域设置
自定义语音服务目前支持下列区域设置中的模型自定义项：

| 模型类型 | 语言支持 |
|----|-----|
| 声学模型 | 美国英语 (en-US) |
| 语言模型 | 美国英语 (en-US)、中文 (zh-CN) |

尽管声学模型自定义项仅支持美国英语，但支持导入中文声学数据以用于离线测试自定义的中文语言模型。

在执行任何操作之前，必须选择适当的区域设置。 当前区域设置在所有数据、模型和部署页面的表标题中指示。 若要更改区域设置，请单击位于表标题下的“更改区域设置”按钮。 这会将你带到区域设置确认页面。 单击“确定”以返回到表。

你应跟进后续步骤
* 了解[如何创建自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)以提高识别准确性
* 了解[如何创建自定义语言模型](cognitive-services-custom-speech-create-language-model.md)以提高识别率
* 按照[听录准则](cognitive-services-custom-speech-transcription-guidelines.md)准备数据
