---
title: 有关文本分析 API 的常见问题解答 - Azure 认知服务 | Microsoft Docs
description: 在 Azure 上获得有关 Microsoft 认知服务文本分析 API 的常见问题解答。
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: bf82899b4317f0f5ce0f6ca5096dccef7cddd931
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366658"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>有关文本分析 API 的常见问题解答 (FAQ)

 在 Azure 上找到与 Microsoft 认知服务文本分析 API 有关的概念、代码和方案相关的常见问题解答。

## <a name="can-text-analytics-identify-sarcasm"></a>文本分析能否识别嘲讽？

分析针对积极消极情绪，而不是进行情绪检测。

情绪分析总是存在一定程度的不精确性，但是当内容没有隐藏的含义或潜台词时，该模型最有用。 反讽、嘲讽、幽默和类似的微妙内容都依赖于文化背景和规范来传达意向。 这种类型的内容是最难分析的。 通常，分析器产生的给定分数与人的主观评估之间的最大差异在于具有微妙含意的内容。

## <a name="can-i-add-my-own-training-data-or-models"></a>我可以添加自己的训练数据或模型吗？

不可以，模型是预先训练的。 对上传数据的唯一可用操作是评分、关键短语提取和语言检测。 我们不托管自定义模型。 如果想要创建并托管自定义机器学习模型，请考虑 [Microsoft R Server 中的机器学习功能](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)。

## <a name="can-i-request-additional-languages"></a>我可以请求其他语言吗？

情绪分析和关键短语提取可用于[部分语言](text-analytics-supported-languages.md)。 自然语言处理很复杂，需要进行大量测试才能发布新功能。 出于这个原因，我们避免预先宣布支持，这样就不会有人依赖需要更多时间才能成熟的功能。 

为帮助我们确定下一步要处理哪些语言的优先顺序，请在[用户之声](https://cognitive.uservoice.com/forums/555922-text-analytics)上为特定语言投票。 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>为什么关键短语提取会返回某些单词而不返回其他单词？

关键短语提取消除了非必要词和独立形容词。 形容词 - 名词组合（例如“壮观的景色”或“有雾的天气”）将一起返回。

通常，输出由名词和句子的宾语组成。 输出按重要性顺序列出，第一个短语是最重要的。 重要性按提及特定概念的次数或该元素与文本中其他元素的关系来衡量。

## <a name="why-does-output-vary-given-identical-inputs"></a>为什么给定相同的输入，输出会不同？

如果更改较大，则会宣布对模型和算法进行改进；如果更新很小，则会悄悄地将其整合到服务中。 随着时间的推移，你可能会发现相同的文本输入会产生不同的情绪分数或关键短语输出。 这是在云中使用托管机器学习资源的正常且有意的结果。

## <a name="next-steps"></a>后续步骤

问题是否与缺少功能相关？ 请考虑在我们的 [UserVoice 网站](https://cognitive.uservoice.com/forums/555922-text-analytics)上为其发起请求或投票。

## <a name="see-also"></a>另请参阅

 [StackOverflow：文本分析 API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow：认知服务](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
