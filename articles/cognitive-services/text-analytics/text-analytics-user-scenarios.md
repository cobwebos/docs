---
title: 文本分析 API 的示例用户方案
titleSuffix: Azure Cognitive Services
description: 使用本文来了解有关将文本分析 API 集成到服务和流程的一些常见方案。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 027e6ec829e9de9956451e48e5f9e1cdd749f9f7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689334"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>文本分析 API 的示例用户方案

文本分析 API 是一个基于云的服务，提供基于文本的高级自然语言处理。 本文将介绍有关将该 API 集成到业务解决方案和流程的一些示例用例。 

## <a name="analyze-survey-results"></a>分析调查结果

使用情绪分析处理原始文本响应，从客户和员工调查结果中提取见解。 聚合分析结果，跟进趋势，并提升客户参与度。

![描述如何对客户和员工调查执行情绪分析的插图。](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>分析录制的客户来电

使用文本转语音、情绪分析和关键短语提取从客户服务通话中提取见解。 在 Power BI 仪表板或门户中显示结果，以便更好地了解客户，突出显示客户服务趋势，并提升客户参与度。 以批的形式发送 API 请求以生成报告，或者进行实时干预。 请参阅 [GitHub 上的](https://github.com/rlagh2/callcenteranalytics)示例代码。

![描述如何使用情绪分析从客户服务通话中自动获取见解的插图](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>处理和分类支持事件

使用关键短语提取和实体识别来处理以非结构化文本格式提交的支持请求。 使用提取的短语和实体将请求分类，以进行资源规划和趋势分析。

![描述如何使用关键短语提取和实体识别分类事件报告与趋势的插图](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>监视产品的社交媒体源

在产品的 Twitter 或 Facebook 页面监视用户对产品提供的反馈。 使用数据来分析客户对新推出产品的情绪，提取有关功能和功能请求的关键短语，或者在出现客户投诉时解决投诉。 请参阅 [Microsoft Flow 模板](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)示例。

![描述如何使用关键短语提取在社交媒体上监视产品和公司反馈的插图](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)
* [使用客户端库将请求发送到文本分析 API](quickstarts/text-analytics-sdk.md)
