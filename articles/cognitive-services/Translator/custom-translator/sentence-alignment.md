---
title: 句子配对和对齐 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 在执行训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译每次学习一个句子的翻译，并通过读取另一个句子来获取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: adbc21c3e963a98a8482de0c26bf5e257f43013e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675453"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>并行文档中的句子配对和对齐

在训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译会报告它能够配对为每个数据集中“已对齐句子”的句子数。

## <a name="pairing-and-alignment-process"></a>配对和对齐过程

自定义翻译每次学习一个句子的翻译。 它从源中读取某个句子，然后从目标中读取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。 此过程可让自定义翻译将一个句子中的单词和短语映射到此句子的翻译中的对应单词和短语。 对齐的目的是尽量确保基于相互翻译的句子训练系统。

## <a name="pre-aligned-documents"></a>预先对齐的文档

如果你知道自己可以提供并行文档，则可以通过提供预先对齐的文本文件来替代句子对齐。 可将两个文档中的所有句子提取到该文本文件，按每行一个句子的方式进行组织，然后使用 `.align` 扩展上传。 `.align` 扩展告知自定义翻译应该跳过句子对齐。

为获得最佳结果，请尽量确保在文件中每行放置一个句子。 句子中没有换行符，因为这会导致对齐不佳。

## <a name="suggested-minimum-number-of-sentences"></a>建议的最小句子数

若要成功完成培训，下表显示了每种文档类型所需的最小句子数。 此限制是一种安全网络，确保你的并行句子包含足够的独特词汇来成功地训练翻译模型。 一般原则是，多个域内并行句子的人工翻译质量应该生成更高质量的模型。

| 文档类型   | 建议的最小句子计数 | 最大句子计数 |
|------------|--------------------------------------------|--------------------------------|
| 培训   | 10,000                                     | 没有上限                 |
| 优化     | 5,000                                      | 2,500                          |
| 测试    | 5,000                                      | 2,500                          |
| 字典 | 0                                          | 没有上限                 |

> [!NOTE]
> - 如果未满足定型的10000最小句子计数，则训练将不会启动，并且会失败。 
> - 优化和测试是可选的。 如果未提供这些值，系统将从培训中删除适当的百分比用于验证和测试。 
> - 可以仅使用字典数据来训练模型。 请参阅[什么是字典](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)。

## <a name="next-steps"></a>后续步骤

- 了解如何在自定义翻译中使用[字典](what-is-dictionary.md)。
