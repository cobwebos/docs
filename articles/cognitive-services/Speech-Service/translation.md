---
title: 翻译示例 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 语音翻译的示例如下。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028381"
---
# <a name="sample-for-translation"></a>翻译示例

> [!NOTE]
> 有关此示例及其他示例的下载说明，请参阅[语音 SDK 示例](samples.md)。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 对于下述所有示例，应进行以下顶级声明：
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>使用麦克风进行翻译

下面的代码片段展示了如何将语音输入从英语翻译成德语并获得翻译文本的语音输出。 该代码片段使用麦克风。

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>使用文件输入进行翻译

下面的代码片段展示了如何将语音输入从英语翻译成德语和法语。
该代码片段使用文件作为输入。

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>示例源代码

最新版本和更高级的示例位于专用的 [GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="next-steps"></a>后续步骤

- [语音识别](./speech-to-text-sample.md)

- [意向识别](./intent.md)
