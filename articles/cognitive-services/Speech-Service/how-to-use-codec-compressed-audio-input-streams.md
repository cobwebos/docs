---
title: 使用语音 SDK 流式传输编解码器压缩的音频 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 将压缩音频流式传输到语音服务。 适用于用于 Linux 的 C++、C# 和 Java，Android 中的 Java 和 iOS 中的 Objective-C。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
zone_pivot_groups: programming-languages-set-twelve
ms.openlocfilehash: 13cb35ffa650661da2855787279c4bdc37126ac9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585007"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>在语音 SDK 中使用编解码器压缩的音频输入

语音服务 SDK 压缩音频输入流  API 提供了一种使用 `PullStream` 或 `PushStream` 将压缩音频流式传输到语音服务的方法。

当前支持对 c #、c + +、Windows 上的 Java （UWP 应用程序不受支持）和 Linux （Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7/8、CentOS 7/8）进行流式处理压缩的输入音频。 Android 中的 Java 和 iOS 平台中的 Objective-C 也支持该功能。
* RHEL 8 和 CentOS 8 需要 Speech SDK 版本1.10.0 或更高版本
* Windows 需要语音 SDK 版本 1.11.0 或更高版本。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>先决条件

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>使用编解码器压缩的音频输入的示例代码

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-objectivec"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/objectivec/examples.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何识别语音](quickstarts/speech-to-text-from-microphone.md)
