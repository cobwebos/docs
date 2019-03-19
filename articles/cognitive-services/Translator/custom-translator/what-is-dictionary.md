---
title: 什么是字典？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 字典一个对齐的文档，其中指定了你始终希望 Microsoft Translator 以相同方式翻译的短语或句子（及其译文）的列表。 字典有时也称为词汇表或术语库。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: bfefb1fe44959bc7e5186a0f14813f41256cf2d5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775435"
---
# <a name="what-is-a-dictionary"></a>什么是字典？

字典是一组对齐的文档，其中指定了短语或句子的列表及其对应的译文。 如果希望 Microsoft Translator 始终使用字典中提供的译文来翻译源短语或句子，可以在训练中使用字典。 字典有时称为词汇表或术语库。 可将字典视为所列的所有字词的强行“复制并替换”译法。

字典仅适用于采用完全受支持 Microsoft 神经机器翻译 (NMT) 系统的语言对的项目。 [查看语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)。

## <a name="phrase-dictionary"></a>短语字典
如果在训练模型时包含短语字典，将按指定的方式翻译所列的任何单词或短语。 句子的余下部分将按平时的方式翻译。 可以使用短语字典来指定不应翻译的短语：在字典中的源和目标文件内提供相同的无需翻译的短语即可。

## <a name="sentence-dictionary"></a>句子字典
使用句子字典可以指定源句子的确切目标译文。 若要进行句子字典匹配，提交的整个句子必须与源字典条目匹配。  如果只是句子的一部分匹配，则该条目不匹配。  检测到匹配项时，将返回句子字典的目标条目。

## <a name="dictionary-only-trainings"></a>仅限字典的训练
可以仅使用字典数据来训练模型。 为此，请仅选择要包含的一个或多个字典文档，然后点击“创建模型”。 由于这是仅限字典的训练，因此无需指定最小训练句子数。 模型完成该训练的速度通常比标准训练要快得多。  最终的模型将使用 Microsoft 基准模型根据添加的字典进行翻译。  不会生成测试报告。

>[!Note]
>自定义翻译不会在字典文件中进行句子对齐，因此，必须确保字典文档中的源和目标短语/句子数相同，并且它们已准确对齐。

## <a name="recommendations"></a>建议

- 字典不可取代使用训练数据训练的模型。  字典本质上只是查找并替换单词或句子。  让系统从完整句子中的训练材料进行学习，通常比使用字典的做法更好。
- 应谨慎使用短语字典。 如果替换了某个句子中的短语，该句子中的上下文将会丢失，或者仅限用于翻译该句子的余下部分。 结果是，尽管会根据短语字典翻译该句子中的短语或单词，但句子的整体翻译质量往往会降低。
- 短语字典非常适合用于复合名词，例如产品名称（“Microsoft SQL Server”）、专有名词（“汉堡市”）或产品功能（“数据透视表”）。 对于动词或形容词，它不能起到相同的作用，因为这些词语的词尾在源或目标语言中很容易发生变化。 避免对复合名词以外的任何内容使用短语字典条目。
- 使用字典时，译文中的大小写和标点符号将反映目标文件中提供的大小写和标点符号。 尝试识别字典文件中输入句子与源句子之间的匹配项时，将忽略大小写和标点符号。 例如，假设要训练一个使用字典的英语到西班牙语翻译系统，该字典在源文件中指定了“City of Hamburg”，在目标文件中指定了“Ciudad de hamburg”。 如果请求翻译包含短语“city of Hamburg”的句子，则“city of Hamburg”将匹配字典文件中的“City of Hamburg”条目，并映射到最终译文中的“Ciudad de hamburg”。
- 如果某个单词在字典文件中多次出现，系统始终使用提供的最后一个条目。 字典不应包含同一单词的多种译文。

## <a name="next-steps"></a>后续步骤

- 阅读[有关文档格式的指导原则](document-formats-naming-convention.md)。
