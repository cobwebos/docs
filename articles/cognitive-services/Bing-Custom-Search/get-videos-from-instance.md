---
title: 从自定义视图中获取视频 - 必应自定义搜索
titlesuffix: Azure Cognitive Services
description: 有关使用必应自定义搜索从 Web 的自定义视图获取视频的简要概述。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 9ddf89ab0c4c14743206b2baf207ec10fee033b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66390322"
---
# <a name="get-videos-from-your-custom-view"></a>从自定义视图获取视频

必应自定义视频搜索可以让你使用视频来丰富自定义搜索体验。 与 Web 结果类似，自定义搜索支持在实例的网站列表中搜索视频。 可以使用必应自定义视频搜索 API 或通过托管 UI 功能获取视频。 使用托管 UI 功能简单易行，并建议用于在短时间内启动和运行搜索体验。 有关配置托管 UI 以包含视频的信息，请参阅[配置托管 UI 体验](hosted-ui.md)。

如果想要更好地控制搜索结果的显示，可以使用必应自定义视频搜索 API。 由于调用该 API 与调用必应视频搜索 API 相似，因此请查看[必应视频搜索](../Bing-Video-Search/search-the-web.md)获取调用该 API 的示例。 但在执行此操作之前，应自行熟悉[自定义视频搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)内容。 主要区别在于支持的查询参数（必须包含 customConfig 查询参数）和向其发送请求的终结点。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
