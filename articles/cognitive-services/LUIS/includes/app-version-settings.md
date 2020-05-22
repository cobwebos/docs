---
title: include 文件
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590974"
---
|级别|UI 设置|API 设置|信息|
|--|--|--|--|
|应用|使终结点成为公共终结点|`Public`|任何人只要拥有预测密钥并知晓你的应用 ID，就可以访问你的公共应用。 |
|版本|使用非确定性定型|`UseAllTrainingData`|训练使用一小部分负采样。 如果要使用所有数据而不是小型负采样，请设置为 `true`。 |
|版本|规范化音调符号|`NormalizeDiacritics`|规范化音调符号在言语中使用音调符号，将字符替换为常规字符。|
|版本|规范化标点|`NormalizePunctuation`|规范化标点表示在你的模型获得定型之前，以及在终结点查询获得预测之前，将从言语中删除标点。|
|版本|规范化 Word 窗体|`NormalizeWordForm`|忽略根以外的 Word 窗体。|

了解规范化[概念](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)以及如何使用[应用](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e)和[版本](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API 更新这些设置或使用 LUIS 门户中的“管理”部分的“应用程序设置”页面。