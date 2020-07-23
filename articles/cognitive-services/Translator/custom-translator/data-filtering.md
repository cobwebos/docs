---
title: 数据筛选 - 自定义翻译器
titleSuffix: Azure Cognitive Services
description: 提交用于训练自定义系统的文档时，这些文档需要经历一系列的处理和筛选步骤，为训练做准备。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 67807e18559006b7d7eb6089a30370d614aefca3
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992824"
---
# <a name="data-filtering"></a>数据筛选

提交用于训练自定义系统的文档时，这些文档需要经历一系列的处理和筛选步骤，为训练做准备。 这些步骤会在这里进行介绍。 了解筛选有助于你了解自定义翻译器中显示的句子计数，以及在进行文档准备以便使用自定义翻译器进行训练时可能需要自行采取的步骤。

## <a name="sentence-alignment"></a>句子对齐
如果文档不是 XLIFF、TMX 或 ALIGN 格式，则自定义翻译器会将源文档和目标文档的句子一句句地互相对齐。 翻译器并不执行文档对齐操作，而是根据文档的命名找出另一语言的匹配文档。 在文档中，自定义翻译器会尝试找出另一语言的相应句子。 它使用类似于嵌入式 HTML 标记的文档标记来帮助进行对齐。  

如果在源和目标端文档中的句子数与目标端文档中的句子数之间存在较大的差异，可能是因为你的文档可能没有在第一位置并行，或者无法对齐其他原因。 如果文档配对时每侧的句子存在大的差异 (>10%)，则必须再次进行查看，确保这些句子确实已对齐。 如果句子计数差异令人怀疑，自定义翻译器会在文档旁边显示一个警告。  


## <a name="deduplication"></a>重复数据删除
自定义翻译器会删除存在于测试中的会根据训练数据来优化文档的句子。删除操作在训练运行中动态进行，不在数据处理步骤中进行。 自定义翻译器会在进行此类删除之前，在项目概览中将句子计数报告给你。  

## <a name="length-filter"></a>长度筛选器
* 删除任意一侧的只有一个单词的句子。
* 删除任意一侧的包含 100 多个单词的句子。中文、日语和朝鲜语除外。
* 删除不到 3 个字符的句子。 中文、日语和朝鲜语除外。
* 删除中文、日语、朝鲜语中超出 2000 个字符的句子。
* 删除字母字符数不到 1% 的句子。
* 删除包含 50 多个单词的字典条目。

## <a name="white-space"></a>空格
* 将任何序列的空格字符（包括制表符和 CR/LF 序列）替换为单个空格的字符。
* 删除句子中的前导或尾随空格

## <a name="sentence-end-punctuation"></a>句末标点
将多个句末标点字符替换为单个实例。  

## <a name="japanese-character-normalization"></a>日语字符规范化
将全角字母和数字转换为半角字符。

## <a name="unescaped-xml-tags"></a>非转义的 XML 标记
筛选会将非转义的标记转换为转义的标记：
* `&lt;` 变为 `&amp;lt;`
* `&gt;` 变为 `&amp;gt;`
* `&amp;` 变为 `&amp;amp;`

## <a name="invalid-characters"></a>无效字符
自定义翻译器会删除包含 Unicode 字符 U+FFFD 的句子。 字符 U+FFFD 表示编码转换失败。

## <a name="next-steps"></a>后续步骤

- 在自定义翻译器中[训练模型](how-to-train-model.md)。
