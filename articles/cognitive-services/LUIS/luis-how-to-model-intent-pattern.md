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
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 39b56c5e73c8ce85a020402dafb622b90c536a1e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143826"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何添加模式以提高预测准确性
在 LUIS 应用接收到终结点话语后，请使用[模式](luis-concept-patterns.md)提高话语（按字词顺序和字词选择呈现模式）的预测准确性。 模式使用特定[语法](luis-concept-patterns.md#pattern-syntax)来指示的位置：[实体](luis-concept-entity-types.md)、实体[角色](luis-concept-roles.md)和可选文本。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-template-utterance-to-create-pattern"></a>添加模板表述以创建模式

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
