---
title: 描述符-LUIS
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 添加应用功能，可以改进对类别和模式的意向和实体的检测或预测
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123147"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>使用描述符提升单词列表的信号

可以将功能添加到 LUIS 应用以提高其准确性。 特征通过提供某些字词和短语是应用域词汇表的一部分的提示来帮助 LUIS。 

[描述符](luis-concept-feature.md)（短语列表）包括属于同一类的一组值（词或短语），并且必须按类似方式处理（例如，城市或产品的名称）。 LUIS 对其中一个值的了解也自动应用到其他值。 此列表的内容与匹配的单词的[列表实体](reference-entity-list.md)（精确文本匹配）不同。

描述符将添加到应用域的词汇，作为 LUIS 的第二个信号。

查看[功能概念](luis-concept-feature.md)以了解何时以及为何使用描述符。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>添加描述符

1. 在 **"我的应用**" 页面上单击应用程序的名称以打开应用程序，然后单击 "**生成**"，然后在应用的左侧面板中单击 "**描述符**"。 

1. 在 "**描述符**" 页上，单击 " **+ 添加描述符**"。 
 
1. 在 "**创建新短语列表描述符**" 对话框中，输入描述符的名称，如 `Cities`。 在 "**值**" 框中，键入描述符的值，例如 "`Seattle`"。 可以一次键入一个值或者用逗号分隔的一组值，然后按 Enter。

    > [!div class="mx-imgBorder"]
    > ![添加描述符城市](./media/luis-add-features/add-phrase-list-cities.png)

    为 LUIS 输入足够值后，将显示 "建议"。 你可以 **+ 添加所有**建议值，也可以选择单个字词。

1. 如果添加的描述符值是可互换使用的替代项，请选择 "保持**这些值可互换**"。

1. 选择“完成”。 新描述符将添加到 "**描述符**" 页。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 你可以从 "**描述符**" 页上的上下文工具栏中删除或停用描述符。

## <a name="next-steps"></a>后续步骤

添加、编辑、删除或停用描述符后，再次[训练并测试应用](luis-interactive-test.md)，以查看性能是否提高。
