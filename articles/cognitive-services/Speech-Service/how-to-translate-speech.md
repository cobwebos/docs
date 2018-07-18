---
title: 使用语音服务翻译语音 | Microsoft Docs
description: 了解如何使用语音服务中的语音翻译。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "35366999"
---
# <a name="translate-speech-using-speech-service"></a>使用语音服务翻译语音

[语音 SDK](speech-sdk.md) 是在应用程序中使用语音翻译的最简单方法。 该 SDK 提供该服务的完整功能。 执行语音翻译的基本过程包括以下步骤：

1. 创建语音工厂，并提供语音服务订阅密钥或授权令牌。 还可在此时配置资源和目标翻译语言，以及指定需要文本输出还是语音输出。

2. 从工厂获取识别器。 对于翻译，请选择翻译识别器。 （其他识别器适用于“语音转文本”。）提供多种风格的翻译识别器，请基于使用的音频源进行选择。

4. 如果需要，可捆绑事件以进行异步操作。 识别器将在其具有中间和最终结果时调用事件处理程序。 否则，应用程序将收到最终翻译结果。

5. 开始识别和翻译。

# <a name="sdk-samples"></a>SDK 示例

有关最新的示例集，请参阅[认知服务语音 SDK 示例 GitHub 存储库](https://aka.ms/csspeech/samples)。

# <a name="next-steps"></a>后续步骤

- [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [在 C# 中识别语音](quickstart-csharp-windows.md)
