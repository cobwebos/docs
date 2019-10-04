---
title: 什么是字典？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 字典一个对齐的文档，其中指定了你始终希望 Microsoft Translator 以相同方式翻译的短语或句子（及其译文）的列表。 字典有时也称为词汇表或术语库。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a4aac8afb7974be402ee98bb65c920133d4c118f
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947689"
---
# <a name="what-is-a-dictionary"></a>什么是字典？

字典是一组对齐的文档，其中指定了短语或句子的列表及其对应的译文。 如果希望 Microsoft Translator 始终使用字典中提供的译文来翻译源短语或句子，可以在训练中使用字典。 字典有时称为词汇表或术语库。 可将字典视为所列的所有字词的强行“复制并替换”译法。 此外，Microsoft 自定义转换器服务构建并使用其自己的常规用途字典，提高翻译质量。 但是，客户提供的字典采用引用单元格，并将首先搜索以查找单词或句子。

字典仅适用于语言对中的项目，这些项目具有完全受支持的 Microsoft 常规神经网络模型。 [查看语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)。

## <a name="phrase-dictionary"></a>短语字典
如果在训练模型时包含短语字典，将按指定的方式翻译所列的任何单词或短语。 句子的余下部分将按平时的方式翻译。 可以使用短语字典来指定不应翻译的短语：在字典中的源和目标文件内提供相同的无需翻译的短语即可。

## <a name="sentence-dictionary"></a>句子字典
使用句子字典可以指定源句子的确切目标译文。 若要进行句子字典匹配，提交的整个句子必须与源字典条目匹配。  如果只是句子的一部分匹配，则该条目不匹配。  检测到匹配项时，将返回句子字典的目标条目。

## <a name="dictionary-only-trainings"></a>仅限字典的训练
可以仅使用字典数据来训练模型。 为此，请仅选择要包含的一个或多个字典文档，然后点击“创建模型”。 由于这是仅限字典的训练，因此无需指定最小训练句子数。 模型完成该训练的速度通常比标准训练要快得多。  最终的模型将使用 Microsoft 基准模型根据添加的字典进行翻译。  不会生成测试报告。

>[!Note]
>自定义翻译不会在字典文件中进行句子对齐，因此，必须确保字典文档中的源和目标短语/句子数相同，并且它们已准确对齐。

## <a name="recommendations"></a>建议

- 字典不是使用定型数据训练模型的替代方案。 建议避免使用这些方法，并让系统从定型数据中学习。 但是，如果必须按原样呈现句子或复合名词，请使用字典。
- 应谨慎使用短语字典。 因此，请注意，当替换句子中的短语时，该句子的上下文会丢失或限制为转换句子的其余部分。 结果是，尽管句子中的短语或字词会根据提供的字典进行转换，但句子的整体翻译质量通常会受到影响。
- 短语字典非常适合用于复合名词，例如产品名称（“Microsoft SQL Server”）、专有名词（“汉堡市”）或产品功能（“数据透视表”）。 对于动词或形容词，它不能起到相同的作用，因为这些词语的词尾在源或目标语言中很容易发生变化。 最佳做法是避免短语字典条目用于除复合名词以外的任何内容。
- 使用字典时，大小写和标点符号非常重要。 字典条目只匹配与字典中的条目具有相同大小写和标点的单词和短语。 你的翻译将反映字典文件的目标端提供的大小写和标点。 例如，你已使用在源文件中指定了 "hello" 的字典向目标文件中的 "布宜诺斯艾利斯 dias" 进行了训练。 当你请求包含 "hello" 的句子的翻译时，系统将首先搜索你的字典，并找到匹配项（"hello"），并将在最终翻译中返回 "布宜诺斯艾利斯 dias"。
- 如果某个单词在字典文件中多次出现，系统始终使用提供的最后一个条目。 因此，字典不应包含相同单词的多个翻译。

## <a name="next-steps"></a>后续步骤

- 阅读[有关文档格式的指导原则](document-formats-naming-convention.md)。
