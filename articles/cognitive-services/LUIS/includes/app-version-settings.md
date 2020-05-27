---
title: include 文件
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653205"
---
了解规范化[概念](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)以及如何使用[版本](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API 更新这些设置或使用 LUIS 门户中的“管理”部分的“设置”页面。


|UI 设置|API 设置|信息|
|--|--|--|
|使用非确定性定型|`UseAllTrainingData`|训练使用一小部分负采样。 如果要使用所有数据而不是小型负采样，请设置为 `true`。 |
|规范化音调符号|`NormalizeDiacritics`|规范化音调符号在言语中使用音调符号，将字符替换为常规字符。 此设置仅适用于支持音调符号的[语言](../luis-reference-application-settings.md#diacritics-normalization)。|
|规范化标点|`NormalizePunctuation`|规范化标点表示在你的模型获得定型之前，以及在终结点查询获得预测之前，将从言语中删除标点。|
|规范化 Word 窗体|`NormalizeWordForm`|忽略根以外的 Word 窗体。|
