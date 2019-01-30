---
title: 限制
titleSuffix: Language Understanding - Azure Cognitive Services
description: 本文介绍 Azure 认知服务语言理解 (LUIS) 的已知限制。 LUIS 具有多个边界区域。 模型边界可控制 LUIS 中的意向、实体和功能。 基于密钥类型的配额限制。 键盘组合可控制 LUIS 网站。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7f8f4848b7181ad3df7ad4fa009ff284de381b75
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820404"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>LUIS 模型和密钥的边界
LUIS 具有多个边界区域。 第一个是[模型边界](#model-boundaries)，它可控制 LUIS 中的意向、实体和功能。 第二个是基于密钥类型的[配额限制](#key-limits)。 边界的第三个区域是用于控制 LUIS 网站的[键盘组合](#keyboard-controls)。 第四个是 LUIS 创作网站和 LUIS [终结点](luis-glossary.md#endpoint) API 之间的[世界区域映射](luis-reference-regions.md)。 


## <a name="model-boundaries"></a>模型边界


|区域|限制|
|--|:--|--|
| [应用名称][luis-get-started-create-app] | *默认最大字符长度 |
| [批处理测试][batch-testing]| 10 个数据集，每个数据集 1000 条话语|
| 显式列表 | 每个应用程序 50 个实体|
| [意向][intents]|每个应用程序 500 个<br>[基于调度](https://aka.ms/dispatch-tool) 的应用程序相应的有 500 个调度源|
| [列表实体](./luis-concept-entity-types.md) | 父级：50 个，子级：20,000 项。 规范名称是 *默认最大字符长度。同义词值没有长度限制。 |
| [机器学习实体](./luis-concept-entity-types.md)：<br> 复合、<br>  分层实体<br> 简单|100 <br>机器学习实体（简单、分层及复合实体）的总数不能超过 100。 复合实体和分层实体拥有的子级不能超过 10 个。  |
| [模式](luis-concept-patterns.md)|每个应用程序 500 个模式。<br>模式的最大长度为 400 个字符。<br>每个模式 3 个 Pattern.any 实体<br>模式中最多 2 个嵌套可选文本|
| [Pattern.any](./luis-concept-entity-types.md)|每个应用程序 100 个，每个模式 3 个 pattern.any 实体 |
| [短语列表][phrase-list]|10 个短语列表，每个列表 5,000 项|
| [预生成实体](./luis-prebuilt-entities.md) | 无限制|
| [正则表达式实体](./luis-concept-entity-types.md)|20 个实体<br>每个正则表达式实体模式 最多 500 个字符|
| [角色](luis-concept-roles.md)|每个应用程序 300 个角色。 每个实体 10 个角色|
| [单条话语][utterances] | 500 个字符|
| [话语][utterances] | 每个应用程序 15,000 条|
| [版本](luis-concept-version.md)| 无限制 |
| [版本名称][luis-how-to-manage-versions] | 10 个字符，仅限字母数字和句点 (.) |

*默认最大字符长度为 50 个字符。 

## <a name="intent-and-entity-naming"></a>意向和实体命名
意向和实体名称中不要使用以下字符：

|Character|名称|
|--|--|
|`{`|左大括号|
|`}`|右大括号|
|`[`|左中括号|
|`]`|右中括号|
|`\`|反斜杠|

## <a name="key-usage"></a>密钥使用情况

语言理解拥有单独的密钥，一类用于创作，另一类用于查询预测终结点。 若要详细了解密钥类型之间的差异，请参阅 [LUIS 中的创作密钥和查询预测终结点密钥](luis-concept-keys.md)。

## <a name="key-limits"></a>密钥限制

创作密钥对于创作和终结点有不同的限制。 LUIS 服务终结点密钥仅对终结点查询有效。


|密钥|创作|终结点|目的|
|--|--|--|--|
|语言理解创作/入门|1 百万/月，5/秒|1 千/月，5/秒|创作 LUIS 应用|
|语言理解[订阅][pricing] - F0 - 免费层 |无效|1 万/月，5/秒|查询 LUIS 终结点|
|语言理解[订阅][pricing] - S0 - 基本层|无效|50/秒|查询 LUIS 终结点|
|认知服务[订阅][ pricing] - S0 - 标准层|无效|50/秒|查询 LUIS 终结点|
|[情绪分析集成](luis-how-to-publish-app.md#enable-sentiment-analysis)|无效|免费|添加情绪信息，包括关键短语数据提取 |
|语音集成|无效|5.50 美元/1 千终结点请求|将口语话语转为文本话语并返回 LUIS 结果|

## <a name="keyboard-controls"></a>键盘控件

|键盘输入 | 说明 | 
|--|--|
|Control+E|切换话语列表中的令牌和实体|

## <a name="website-sign-in-time-period"></a>网站登录时间段

登录访问权限为 60 分钟。 在此时间段后，会出现此错误。 需重新登录。

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
