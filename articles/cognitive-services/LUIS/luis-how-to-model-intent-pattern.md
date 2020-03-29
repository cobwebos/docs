---
title: 模式添加准确性 - LUIS
titleSuffix: Azure Cognitive Services
description: 在语言理解 (LUIS) 应用程序中添加模式模板，以提高预测准确性。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311710"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何添加模式以提高预测准确性
在 LUIS 应用接收到终结点话语后，请使用[模式](luis-concept-patterns.md)提高话语（按字词顺序和字词选择呈现模式）的预测准确性。 模式使用特定的[语法](luis-concept-patterns.md#pattern-syntax)来指示：[实体](luis-concept-entity-types.md)、实体[角色](luis-concept-roles.md)和可选文本的位置。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> 模式仅包括机器学习实体父项，而不包括子组件。

## <a name="adding-example-utterances-as-pattern"></a>将示例陈述添加为模式

如果要为实体添加模式，_最简单的_方法是从"意向详细信息"页创建模式。 这可确保语法与示例陈述匹配。

1. 在[预览 LUIS 门户](https://preview.luis.ai)中，从"**我的应用"** 页面选择应用。
1. 在 **"意向"** 列表页上，选择要从中创建模板陈述的示例陈述的意图名称。
1. 在"意向详细信息"页上，选择要用作模板陈述的示例陈述的行，然后从上下文工具栏中选择 **" 添加为模式**"。

    > [!div class="mx-imgBorder"]
    > ![在"意向详细信息"页上选择示例陈述作为模板模式的屏幕截图。](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. 在"弹出窗口"框中，在"**确认模式**"页上选择 **"完成**"。 不需要定义实体的子组件、约束或描述符。 您只需列出机器学习的实体。

    > [!div class="mx-imgBorder"]
    > !["意向详细信息"页上确认示例陈述作为模板模式的屏幕截图。](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. 如果需要使用`[]`（方形）括号编辑模板（如选择文本作为可选文本），则需要从 **"模式"** 页进行此编辑。

1. 在导航栏中，选择 **"训练"** 以使用新模式训练应用。

## <a name="add-template-utterance-using-correct-syntax"></a>使用正确的语法添加模板陈述

1. 通过选择“我的应用”页上的应用名称来打开应用，然后选择“提高应用性能”下左侧面板中的“模式”************。

    > [!div class="mx-imgBorder"]
    > ![模式列表的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. 为模式选择正确意向。

1. 在模板文本框中，键入模板表述，然后按 Enter。 如果要输入实体名称，请使用正确的模式实体语法。 使用以 `{` 开头的实体语法。 实体列表随即显示。 选择正确的实体。

    > [!div class="mx-imgBorder"]
    > ![模式实体的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果实体包含[一个角色](luis-concept-roles.md)，则指示具有单个冒号的角色`:`，在实体名称（如`{Location:Origin}`） 之后， 实体的角色列表将以列表形式显示。 选择该角色，然后按 Enter。

    > [!div class="mx-imgBorder"]
    > ![包含角色的实体的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    选择正确的实体后，请完成模式输入，然后按 Enter。 模式输入完成后，请[培训](luis-how-to-train.md)应用。

    > [!div class="mx-imgBorder"]
    > ![包含两种实体类型的已输入模式的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>在对模型进行模式更改后对应用进行培训
添加、编辑、删除或重新分配模式后，请针对更改来[培训](luis-how-to-train.md)和[发布](luis-how-to-publish-app.md)应用以影响终结点查询。

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>使用上下文工具栏

使用模式列表上方的上下文工具栏可以：

* 搜索模式
* 编辑模式
* 为其他意向重新分配一个模式
* 为其他意向重新分配多个模式
* 删除单个模式
* 删除多个模式
* 按实体筛选模式列表
* 按意向筛选模式列表
* 删除实体或意向筛选器
* 在意向或实体页上通过现有表述添加模式

## <a name="next-steps"></a>后续步骤

* 了解如何按照教程[生成模式](luis-tutorial-pattern.md)（使用 pattern.any 和角色）。
* 了解如何[培训](luis-how-to-train.md)应用。
