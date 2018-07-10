---
title: 支持的语言 - QnA Maker - Azure 认知服务 | Microsoft Docs
description: 了解 QnA Maker 支持哪些语言。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366580"
---
# <a name="supported-languages"></a>支持的语言

知识库的语言影响 QnA Maker 从[源](../Concepts/data-sources-supported.md)自动提取问题和解答的能力，以及 QnA Maker 响应用户查询时提供的结果的相关性。

## <a name="auto-extraction"></a>自动提取
QnA Maker 支持提取任何语言页面中的问题/解答，但对于以下语言，提取有效性更高，因为 QnA Maker 使用关键字来标识问题。

|支持的语言| 区域设置|
|-----|----|
|英语|en-*|
|法语|fr-*|
|意大利语|it-*|
|德语|de-*|
|西班牙语|es-*|

## <a name="query-matching-and-relevance"></a>查询匹配和相关性
QnA Maker 依赖于 Azure 搜索中的[语言分析器](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support)来提供结果。 针对 En-* 语言提供了特殊的重新排名功能以实现更好的相关性。

QnA Maker 在创建期间会自动检测知识库的语言并相应地设置分析器。 可以采用以下语言创建知识库。 有关 QnA Maker 如何处理语言的更多详细信息，请阅读[此文](../How-To/language-knowledge-base.md)。


> [!Tip]
> 语言分析器一旦设置便无法更改。 另外，语言分析器应用于同一 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)中的所有知识库。 如果打算拥有采用不同语言的知识库，则应当在不同的 QnA Maker 服务下创建它们。

|支持的语言|
|-----|
|阿拉伯语|
|亚美尼亚语|，
孟加拉语|
|巴斯克语|
|保加利亚语|
|加泰罗尼亚语|
|中文_简体|
|中文_繁体|
|克罗地亚语|
|捷克语|
|丹麦语|
|荷兰语|
|英语|
|爱沙尼亚语|
|芬兰语|
|法语|
|加利西亚语|
|德语|
|希腊语|
|古吉拉特语|
|希伯来语|
|印地语|
|匈牙利语|
|冰岛语|
|印度尼西亚语|
|爱尔兰语|
|意大利语|
|日语|
|卡纳达语|
|韩语|
|拉脱维亚语|
|立陶宛语|
|马拉雅拉姆语|
|马来语|
|挪威语|
|波兰语|
|葡萄牙语|
|旁遮普语|
|罗马尼亚语|
|俄语|
|塞尔维亚语_西里尔文|
|塞尔维亚语_拉丁语|
|斯洛伐克语|
|斯洛文尼亚语|
|西班牙语|
|瑞典语|
|泰米尔语|
|泰卢固语|
|泰语|
|土耳其语|
|乌克兰语|
|乌尔都语|
|越南语|
