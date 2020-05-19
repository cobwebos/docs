---
title: 从语音翻译 API 迁移到语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何将应用程序从语音翻译 API 迁移到语音服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 305242e13dab23b6a003c5d864073372a052601a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593165"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>从语音翻译 API 迁移到语音服务

本文介绍如何将应用程序从 Microsoft 语音翻译 API 迁移到[语音服务](index.yml)。 本指南概述了语音翻译 API 和语音服务之间的差异，并提供了有关迁移应用程序的策略。

> [!NOTE]
> 语音服务不会接受你的语音翻译 API 订阅密钥。 需要创建新的语音服务订阅。

## <a name="comparison-of-features"></a>功能比较

| 功能                                           | 语音翻译 API                                  | 语音服务 | 详细信息                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 翻译为文本                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 翻译为语音                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 全球终结点                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 语音服务未提供全局终结点。 全球终结点可以自动将流量定向到最近的区域终结点，从而减少应用程序中的延迟。                                                    |
| 区域终结点                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 连接时间限制                             | 90 分钟                                               | 使用 SDK 时，无限制。 使用 WebSocket 连接时，限制为 10 分钟。                                                                                                                                                                                                                                                                                   |
| 头中的身份验证密钥                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 在一个请求中翻译多种语言 | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 可用 SDK                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 请参阅适用于 Sdk 的[语音服务文档](index.yml)。                                                                                                                                                    |
| WebSocket 连接                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 语言 API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | 语音服务支持 "[转换器语言参考](../translator-speech/languages-reference.md)" 一文中所述的相同语言范围。 |
| 猥亵内容筛选器和标记                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM 作为输入                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 其他文件类型作为输入                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 部分结果                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| 计时信息                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| 相关性 ID                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| 自定义语音模型                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 语音服务提供自定义语音模型，使你能够自定义你组织的独特词汇的语音识别。                                                                                                                                           |
| 自定义翻译模型                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | 通过订阅 Microsoft 文本翻译 API，可使用[自定义翻译工具](https://www.microsoft.com/translator/business/customization/)，从而利用自己的数据提高翻译准确度。                                                 |

## <a name="migration-strategies"></a>迁移策略

如果你或你的组织在开发或生产环境中具有使用语音翻译 API 的应用程序，则应将其更新为使用语音服务。 请参阅[语音服务](index.yml)文档，了解可用的 sdk、代码示例和教程。 迁移时请考虑以下事项：

* 语音服务未提供全局终结点。 确定应用程序在使用一个区域终结点处理其所有流量的情况下，能否高效运行。 如果不能，则使用地理位置来确定最高效的终结点。

* 如果应用程序使用长期有效的连接但无法使用可用的 SDK，则可以使用 WebSocket 连接。 通过在适当的时间重新连接来管理 10 分钟的超时限制。

* 如果你的应用程序使用转换器服务并语音翻译 API 启用自定义翻译模型，则可以使用语音服务直接添加类别 Id。

* 与语音翻译 API 不同，语音服务可以通过一个请求完成多种语言的翻译。

## <a name="next-steps"></a>后续步骤

* [免费试用语音服务](get-started.md)
* [快速入门：使用语音 SDK 在 UWP 应用中识别语音](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>请参阅

* [什么是语音服务](overview.md)
* [语音服务和语音 SDK 文档](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
