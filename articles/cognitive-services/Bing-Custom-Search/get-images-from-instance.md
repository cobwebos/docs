---
title: 从自定义视图中获取图像 - 必应自定义搜索
titleSuffix: Azure Cognitive Services
description: 有关使用必应自定义搜索从 Web 的自定义视图获取图像的简要概述。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5fa71c62969e0f1ff7e98f374eca63efa051c041
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390330"
---
# <a name="get-images-from-your-custom-view"></a>从自定义视图获取图像

必应自定义图像搜索可以让你使用图像来丰富自定义搜索体验。 与 Web 结果类似，自定义搜索支持在实例的网站列表中搜索图像。 可以使用必应自定义图像搜索 API 或通过托管 UI 功能获取图像。 使用托管 UI 功能简单易行，并建议用于在短时间内启动和运行搜索体验。  有关配置托管 UI 以包含图像的信息，请参阅[配置托管 UI 体验](hosted-ui.md)。

如果想要更好地控制显示搜索结果，可以使用必应自定义图像搜索 API。 由于调用 API 类似于调用必应图像搜索 API，因此请查看[必应图像搜索](../Bing-Image-Search/overview.md)获取调用 API 的示例。 但在执行此操作之前，应自行熟悉[自定义图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)内容。 主要区别在于支持的查询参数（必须包含 customConfig 查询参数）和向其发送请求的终结点。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
