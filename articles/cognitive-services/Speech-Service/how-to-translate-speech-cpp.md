---
title: 使用适用于 C++ 的语音 SDK 转换语音
titleSuffix: Microsoft Cognitive Services
description: 展示了如何使用适用于 C++ 的语音 SDK 转换语音。
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 98b5339bb98849ee2efcc5c81f9499b10fc38e71
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331219"
---
# <a name="translate-speech-by-using-the-speech-sdk-for-c"></a>使用适用于 C++ 的语音 SDK 转换语音

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!include[Intro](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!include[Intro - top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#toplevel)]

[!include[Intro - using microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-cpp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#TranslationWithMicrophone)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
请在 `samples/cpp/windows/console` 文件夹中查找本文中的代码。

## <a name="next-steps"></a>后续步骤

- [如何识别语音](how-to-recognize-speech-cpp.md)
- [如何从语音中识别意向](how-to-recognize-intents-from-speech-cpp.md)。
