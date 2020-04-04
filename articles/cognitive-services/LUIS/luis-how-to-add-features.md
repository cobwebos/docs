---
title: 描述符 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 添加应用功能，可以改进对类别和模式的意向和实体的检测或预测
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631447"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>使用描述符来增强字词列表的信号

可以将功能添加到 LUIS 应用以提高其准确性。 特征通过提供某些字词和短语是应用域词汇表的一部分的提示来帮助 LUIS。

[描述符](luis-concept-feature.md)（短语列表）包括属于同一类且必须类似处理的值（例如，城市或产品的名称）的一组值（单词或短语）。 LUIS 对其中一个值的了解也自动应用到其他值。 此列表不同于包含匹配字词的[列表实体](reference-entity-list.md)（完全文本匹配）。

描述符添加到应用域的词汇中，作为这些字词的第二个 LUIS 信号。

查看[功能概念](luis-concept-feature.md)，以了解何时以及为什么使用描述符。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>添加描述符

1. 单击“我的应用”页上的名称打开应用，单击“构建”，然后单击应用左侧面板中的“描述符”************。

1. 在“描述符”页上，单击“+ 添加描述符”********。

1. 在“创建新短语列表描述符”**** 对话框中，输入描述符的名称（例如 `Cities`）。 在“值”框中，键入描述符的值，例如 `Seattle`。**** 可以一次键入一个值或者用逗号分隔的一组值，然后按 Enter****。

    > [!div class="mx-imgBorder"]
    > ![添加描述符“Cities”](./media/luis-add-features/add-phrase-list-cities.png)

    为 LUIS 输入足够的值后，会显示建议。 对于建议的值，可以单击“+ 全部添加”，也可选择单个术语****。

1. 如果添加的描述符值是可交换使用的替代值，则让“这些值可以交换”保持选中状态****。

1. 短语列表可以应用于具有**全局**设置的整个应用，也可以应用于特定模型（意图或实体）。 如果创建短语列表，作为意图或实体_的描述符_，则切换设置为非全局。 在这种情况下，切换的含义是描述符只是该模型的一个特征，因此 _，它不是应用程序的全局_。

1. 选择 **"完成**"。 新描述符会添加到“描述符”页****。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 可以在“描述符”**** 页上，删除或取消激活上下文工具栏中的描述符。

## <a name="next-steps"></a>后续步骤

添加、编辑、删除或删除描述符后，再次[训练和测试应用](luis-interactive-test.md)以查看性能是否提高。
