---
title: 常见问题解答 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 本文包含有关 Azure 认知服务自定义转换器的常见问题的解答。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: fefd3fcd82454d505099f83944b0e251b71410f0
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996901"
---
# <a name="custom-translator-frequently-asked-questions"></a>自定义翻译人员常见问题

本文包含有关[自定义翻译](https://portal.customtranslator.azure.ai)的常见问题解答。

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>自定义翻译目前有哪些限制？

在文件大小、模型训练和模型部署方面没有限制。 设置训练以便在自定义翻译中构建模型时，请记住以下限制。

- 提交的文件大小必须小于 100 MB。
- 不支持单语数据。

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>何时应该请求部署已训练的翻译系统？

可能需要经历多次训练才能为项目创建最佳的翻译系统。 如果 BLEU 评分和/或测试结果不令人满意，最好是尝试使用更多的训练数据或更多精心筛选的数据。 设计优化集和测试集时应该严谨且慎重，使之全面代表所要翻译的材料的术语和样式。 可以更自由地撰写训练数据，并体验不同的选项。 如果对系统测试结果中的翻译感到满意，无需将更多数据添加到训练来改善已训练的系统，并且想要通过 API 访问训练的模型，则可以请求系统部署。

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>可以在一个项目中部署多少个已训练的系统？

在每个项目中只能部署一个已训练的系统？ 可能需要经历多次训练才能为项目创建合适的翻译系统，我们鼓励你请求部署可提供最佳结果的训练。 可以参考 BLEU 评分（越高越好）来确定训练的质量，并在确定翻译质量是否适合部署之前咨询评审者。

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>训练的部署预期在多长时间内完成？

部署通常需要花费不到一小时的时间。

## <a name="how-do-you-access-a-deployed-system"></a>如何访问已部署的系统？

通过指定类别 Id，可以通过 Microsoft Translator V3 访问已部署的系统。 有关转换器的详细信息，请参阅[API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)网页。

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>如果我的数据已进行句子对齐，如何跳过对齐和断句？

对于 TMX 文件和扩展名为 `.align` 的文本文件，自定义翻译会跳过句子对齐和断句。 对于已完美对齐且无需进一步处理的 `.align` 文件，用户可以通过某个选项跳过自定义翻译器的断句和对齐处理。 我们建议仅对完美对齐的文件使用 `.align` 扩展名。

如果提取的句子数与具有相同基本名称的两个文件不匹配，自定义翻译仍会针对 `.align` 文件运行句子对齐程序。

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>我已尝试上传 TMX，但出现“文档处理失败”消息。

请确保 TMX 符合 <https://www.gala-global.org/tmx-14b> 中的 TMX 1.4b 规范。
