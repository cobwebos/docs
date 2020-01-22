---
title: 模式添加准确性-LUIS
titleSuffix: Azure Cognitive Services
description: 添加模式模板以提高语言理解（LUIS）应用程序中的预测准确性。
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
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311710"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何添加模式以提高预测准确性
LUIS 应用收到终结点最谈话后，请使用[模式](luis-concept-patterns.md)来改善最谈话的预测准确性，这种模式以字序和 word 选项显示模式。 模式使用特定[语法](luis-concept-patterns.md#pattern-syntax)来指示的位置：[实体](luis-concept-entity-types.md)、实体[角色](luis-concept-roles.md)和可选文本。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> 模式仅包括计算机学习的实体父代，而不包括子组件。

## <a name="adding-example-utterances-as-pattern"></a>添加示例最谈话作为模式

如果要为实体添加模式，_最简单_的方法是从意向详细信息页创建模式。 这可确保语法与示例查询文本匹配。

1. 在[预览版 LUIS 门户](https://preview.luis.ai)中，从 "**我的应用**程序" 页中选择该应用程序。
1. **在 "目的列表"** 页上，选择要从中创建模板查询文本的示例查询文本的意图名称。
1. 在 "目的详细信息" 页上，选择要用作模板查询文本的示例查询文本的行，然后从上下文工具栏中选择 " **+ 添加为模式**"。

    > [!div class="mx-imgBorder"]
    > 在意向详细信息页上选择示例查询文本作为模板模式 ![屏幕截图。](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. 在弹出框中，选择 "**确认模式**" 页上的 "**完成**"。 不需要定义实体的子组件、约束或描述符。 只需列出机器学习的实体。

    > [!div class="mx-imgBorder"]
    > 在意向详细信息页上，![用于确认示例查询文本作为模板模式的屏幕截图。](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. 如果需要编辑模板（例如，选择 "文本" （可选）和 "`[]` （方形）"），则需要从 "**模式**" 页进行此编辑。

1. 在导航栏中，选择 "**训练**" 以新模式训练应用。

## <a name="add-template-utterance-using-correct-syntax"></a>使用正确的语法添加模板查询文本

1. 通过选择“我的应用”页上的应用名称来打开应用，然后选择“提高应用性能”下左侧面板中的“模式”。

    > [!div class="mx-imgBorder"]
    > 模式列表的 ![屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. 为模式选择正确意向。

1. 在模板文本框中，键入模板表述，然后按 Enter。 如果要输入实体名称，请使用正确的模式实体语法。 使用以 `{` 开头的实体语法。 实体列表随即显示。 选择正确的实体。

    > [!div class="mx-imgBorder"]
    > 模式](./media/luis-how-to-model-intent-pattern/patterns-3.png) 的实体 ![屏幕截图

    如果实体包含一个[角色](luis-concept-roles.md)，请在实体名称后指定具有单个冒号 `:`的角色，如 `{Location:Origin}`。 实体的角色列表将以列表形式显示。 选择该角色，然后按 Enter。

    > [!div class="mx-imgBorder"]
    > 具有角色](./media/luis-how-to-model-intent-pattern/patterns-4.png) 的实体 ![屏幕快照

    选择正确的实体后，请完成模式输入，然后按 Enter。 模式输入完成后，请[培训](luis-how-to-train.md)应用。

    > [!div class="mx-imgBorder"]
    > 具有两种类型的实体的已输入模式 ![屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

通过模式列表上方的上下文工具栏，您可以：

* 搜索模式
* 编辑模式
* 为其他意向重新分配一个模式
* 为其他意向重新分配多个模式
* 删除-单模式
* 删除多个模式
* 按实体筛选模式列表
* 筛选器-模式-按意向列出
* 删除实体或意向筛选器
* 在意向或实体页上通过现有表述添加模式

## <a name="next-steps"></a>后续步骤

* 了解如何使用模式[生成模式](luis-tutorial-pattern.md)。任何和具有教程的角色。
* 了解如何[培训](luis-how-to-train.md)应用。
