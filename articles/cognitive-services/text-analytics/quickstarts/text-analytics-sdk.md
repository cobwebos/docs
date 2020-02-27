---
title: 快速入门：文本分析客户端库 v3 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 参考本快速入门，将应用程序连接到 Azure 认知服务的文本分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c6883b9b79adfc4f91af354147d45ea27d806d78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614497"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>快速入门：使用文本分析客户端库

从文本分析客户端库开始操作。 请按照以下步骤安装程序包并试用基本任务的示例代码。

使用文本分析客户端库执行：

* 情绪分析
* 语言检测
* 实体识别
* 关键短语提取

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * 文本分析 API 的最新预览版本为 `3.0-preview`，这包括改进的[情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[命名实体识别 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 的公共预览版。 最新稳定版本为 `2.1`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * 本快速入门适用于文本分析客户端库的 `3.0-preview` 版本，这包括改进的[情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[命名实体识别 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 的公共预览版。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * 文本分析 API 的最新预览版本为 `3.0-preview`，这包括改进的[情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[命名实体识别 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 的公共预览版。 最新稳定版本为 `2.1`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * 文本分析 API 的最新预览版本为 `3.0-preview`，这包括改进的[情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)和[命名实体识别 (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) 的公共预览版。 最新稳定版本为 `2.1`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>其他语言支持

如果已单击此选项卡，则可能看不到采用你偏好的编程语言的快速入门。 别担心，我们提供了其他快速入门。 使用表格查找适用于编程语言的示例。

| 语言 | 可用版本 | 
|----------|------------------------|
| Ruby     | [版本 2.1](ruby-sdk.md) | 
| Go       | [版本 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索解决方案](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
* [检测语言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [语言识别](../how-tos/text-analytics-how-to-language-detection.md)
