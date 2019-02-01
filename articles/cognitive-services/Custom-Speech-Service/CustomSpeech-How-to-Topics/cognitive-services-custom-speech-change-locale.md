---
title: Azure 上的自定义语音服务中支持的区域设置和语言 | Microsoft Docs
description: 认知服务中自定义语音服务的支持语言的概述。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: e5c1f7d9648f404512f366be4f4d16ea0cb0f778
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217112"
---
# <a name="supported-locales-in-custom-speech-service"></a>自定义语音服务中支持的区域设置

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

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
