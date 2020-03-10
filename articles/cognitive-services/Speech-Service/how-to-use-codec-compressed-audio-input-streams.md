---
title: 通过 Speech SDK-语音服务流式处理编解码器压缩音频
titleSuffix: Azure Cognitive Services
description: 了解如何通过语音 SDK 将压缩音频流式传输到语音服务。 适用于C++适用C#于 Linux 的、和 java、Android 中的 java 和 iOS 中的目标-C。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 3fab02d3dc567a2c54edad5bfb05abe7d99f7b7c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943807"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>通过语音 SDK 使用编解码器压缩的音频输入

语音服务 SDK**压缩的音频输入流**API 提供一种使用 `PullStream` 或 `PushStream`将压缩音频流式传输到语音服务的方法。

> [!IMPORTANT]
> 当前支持流式传输的C#输入音频， C++在 Linux 上为 Java （ubuntu 16.04、ubuntu 18.04、Debian 9、RHEL 8、CentOS 8）。 它在 Android 和 iOS 平台中的适用于 Java 的 Java 也是支持的。
> 需要1.7.0 或更高版本的语音 SDK （RHEL 8、CentOS 8 的版本1.10.0 或更高版本）。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>必备条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-tos/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何识别语音](quickstarts/speech-to-text-from-microphone.md)
