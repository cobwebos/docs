---
title: 用于增强实体检测的短语列表
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 添加应用功能，可以改进对类别和模式的意向和实体的检测或预测
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6831f67a33d114ca5c42ddacf8ef4de704e21711
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036950"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>使用短语列表来增强字词列表的信号

可以将功能添加到 LUIS 应用以提高其准确性。 特征通过提供某些字词和短语是应用域词汇表的一部分的提示来帮助 LUIS。 

## <a name="add-phrase-list"></a>添加短语列表

1. 单击“我的应用”页上的名称打开应用，单击“构建”，然后单击应用左侧面板中的“短语列表”。 

2. 在“短语列表”页上，单击“新建短语列表”。 
 
3. 在“添加短语列表”对话框中，键入“城市”作为短语列表的名称。 在“值”框中，键入短语列表的值。 可以一次键入一个值或者用逗号分隔的一组值，然后按 Enter。

    ![添加短语列表“城市”](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS 可以建议相关值，用于添加到短语列表。 单击“建议”，获取一组与添加的值在语义上相关的建议值。 可以单击任何建议的值，或单击“全部添加”添加所有值。

    ![短语列表建议的值](./media/luis-add-features/related-values.png)

5. 如果添加的短语列表值是可交换使用的替代值，则单击“这些值可以交换”。

    ![短语列表建议的值](./media/luis-add-features/interchangeable.png)

6. 单击“ **保存**”。 将“城市”短语列表添加到“短语列表”页。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 可以单击每个短语列表行末尾的省略号 (***...***) 按钮编辑、删除或停用短语列表。

## <a name="pattern-regular-expression-feature"></a>模式（正则表达式）功能 
此功能已弃用。 新的模式功能无法添加到 LUIS。 对任何现有模式功能的支持将截止到 2018 年 5 月。 有助于将标准 LUIS 正则表达式与 PR 匹配到[识别器-文本 Github 存储库](https://github.com/Microsoft/Recognizers-Text)。 

## <a name="next-steps"></a>后续步骤

添加、编辑、删除或停用短语列表后，再次[定型和测试应用](luis-interactive-test.md)，查看性能是否有所改善。
