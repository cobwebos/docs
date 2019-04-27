---
title: 从必应语音将迁移到 Azure 的语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何从现有必应语音订阅迁移到 Azure 的语音服务。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653710"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>从必应语音迁移到语音服务

本文介绍了如何将应用程序从必应语音 API 迁移到语音服务。

本文概述了必应语音 Api 和语音服务之间的区别，并建议迁移您的应用程序策略。 必应语音 API 订阅密钥不适用于语音服务;你将需要新的语音服务订阅。

单一的语音服务订阅密钥授予对以下功能的访问权限。 每个功能单独计量，以便仅针对你使用的功能收费。

* [语音转文本](speech-to-text.md)
* [自定义语音转文本](https://cris.ai)
* [文本转语音](text-to-speech.md)
* [自定义文本转语音声音](how-to-customize-voice-font.md)
* [语音翻译](speech-translation.md)（不包括[文本翻译](../translator/translator-info-overview.md)）

[语音 SDK](speech-sdk.md) 是必应语音客户端库的功能替换，但使用了不同 API。

## <a name="comparison-of-features"></a>功能比较

语音服务都很大程度上类似于必应语音，具有以下差异。

Feature | 必应语音 | 语音服务 | 详细信息
-|-|-|-
C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | 语音服务支持 Windows 和 Linux。
Java SDK | :heavy_check_mark: | :heavy_check_mark: | 语音服务支持的 Android 和语音设备。
C# SDK | :heavy_check_mark: | :heavy_check_mark: | 语音服务支持 Windows 10、 通用 Windows 平台 (UWP) 和.NET Standard 2.0。
连续语音识别 | 10 分钟 | 无限制（使用 SDK） | 必应语音和语音服务 Websocket 协议支持最多 10 分钟每个调用。 但是，语音 SDK 会在超时或断开时自动重新连接。
部分或中期结果 | :heavy_check_mark: | :heavy_check_mark: | 使用 Websocket 协议或 SDK。
自定义语音模型 | :heavy_check_mark: | :heavy_check_mark: | 必应语音需要单独的自定义语音订阅。
自定义语音字体 | :heavy_check_mark: | :heavy_check_mark: | 必应语音需要单独的自定义语音订阅。
24-KHZ 语音 | :heavy_minus_sign: | :heavy_check_mark:
语音意向识别 | 需要单独的 LUIS API 调用 | 已集成（与 SDK） |  可以将 LUIS 密钥用于语音服务。
简单意向识别 | :heavy_minus_sign: | :heavy_check_mark:
批量听录长音频文件 | :heavy_minus_sign: | :heavy_check_mark:
识别模式 | 通过终结点 URI 手动 | 自动 | 识别模式在语音服务中不可用。
终结点位置 | 全局 | 区域 | 区域终结点改善延迟。
REST API | :heavy_check_mark: | :heavy_check_mark: | 语音服务 REST Api 是与必应语音 （不同终结点） 兼容。 REST API 支持文本转语音以及限制的语音转文本功能。
Websocket 协议 | :heavy_check_mark: | :heavy_check_mark: | 语音服务 Websocket API 适用于必应语音 （不同终结点）。 在可能的情况下迁移到语音 SDK，以简化代码。
服务到服务 API 调用 | :heavy_check_mark: | :heavy_minus_sign: | 通过 C# 服务库在必应语音中提供。
开源 SDK | :heavy_check_mark: | :heavy_minus_sign: |

语音服务使用基于时间的定价模型 （而非事务模式）。 请参阅[语音服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)有关详细信息。

## <a name="migration-strategies"></a>迁移策略

如果你或你的组织有开发或生产环境中使用必应语音 API 的应用程序，应更新其越早越好使用语音服务。 请参阅[语音服务文档](index.yml)可用 Sdk、 代码示例和教程。

语音服务[REST Api](rest-apis.md)与必应语音 Api 兼容。 如果当前正在使用必应语音 REST Api，则需要更改的 REST 终结点时，切换到语音服务订阅密钥。

语音服务 Websocket 协议也是与所使用的必应语音兼容。 对于新的开发，建议使用语音 SDK 而不要使用 WebSocket。 同样，将现有代码迁移到该 SDK 也是一个好主意。 但是，与 REST API 一样，通过 Websocket 使用必应语音的现有代码只需要更改终结点和更新密钥。

如果使用特定编程语言的必应语音客户端库，则迁移到[语音 SDK](speech-sdk.md) 需要更改应用程序，因为 API 不同。 语音 SDK 可以简化代码，同时使你可以访问新功能。

目前，语音 SDK 支持 C#（Windows 10、UWP、.NET Standard）、Java（Android 和自定义设备）、Objective C (iOS)、C++（Windows 和 Linux）以及 JavaScript。 所有平台上的 API 均类似，从而简化了多平台开发。

语音服务不提供全局终结点。 确定应用程序在使用一个区域终结点处理其所有流量的情况下，能否高效运行。 如果不能，则使用地理位置来确定最高效的终结点。 你需要一个单独的语音服务订阅，则使用每个区域中。

如果应用程序使用长期有效的连接但无法使用可用的 SDK，则可以使用 WebSocket 连接。 通过在适当的时间重新连接来管理 10 分钟的超时限制。

语音 SDK 入门：

1. 下载[语音 SDK](speech-sdk.md)。
1. 通过语音服务的工作[快速入门指南](quickstart-csharp-dotnet-windows.md)并[教程](how-to-recognize-intents-from-speech-csharp.md)。 另请查看[代码示例](samples.md)来体验新的 API。
1. 更新应用程序以使用语音服务。

## <a name="support"></a>支持

必应语音客户应通过打开[支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来联系客户支持。 如果你的支持需要一个[技术支持计划](https://azure.microsoft.com/support/plans/)，也可以与我们联系。

语音服务、 SDK 和 API 支持，请访问语音服务[支持页面](support.md)。

## <a name="next-steps"></a>后续步骤

* [免费试用语音服务](get-started.md)
* [快速入门：使用语音 SDK 在 UWP 应用中识别语音](quickstart-csharp-uwp.md)

## <a name="see-also"></a>另请参阅
* [语音服务发行说明](releasenotes.md)
* [什么是语音服务](overview.md)
* [语音服务和语音 SDK 文档](speech-sdk.md#get-the-sdk)
