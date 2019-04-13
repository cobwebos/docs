---
title: 模式添加准确性
titleSuffix: Language Understanding - Azure Cognitive Services
description: 添加模式模板以改进预测精确性语言理解 (LUIS) 应用程序中。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521248"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何添加模式以改进预测精确性
LUIS 应用接收终结点语音样本后，使用[模式](luis-concept-patterns.md)以改进预测精确性的语音样本，显示一个图案中单词顺序和 word 的选择。 模式使用特定[语法](luis-concept-patterns.md#pattern-syntax)以指示的位置：[实体](luis-concept-entity-types.md)，实体[角色](luis-concept-roles.md)，和可选的文本。

## <a name="add-template-utterance-to-create-pattern"></a>添加模板表述以创建模式
1. 通过选择“我的应用”页上的应用名称来打开应用，然后选择“提高应用性能”下左侧面板中的“模式”。

    ![模式列表的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. 为模式选择正确意向。 

    ![选择意向](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. 在模板文本框中，键入模板表述，然后按 Enter。 如果要输入实体名称，请使用正确的模式实体语法。 使用以 `{` 开头的实体语法。 实体列表随即显示。 选择正确的实体，然后按 Enter。 

    ![模式实体的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果你的实体包括[角色](luis-concept-roles.md)，指示角色使用单个冒号`:`，在该实体名称，例如`{Location:Origin}`。 实体的角色列表将以列表形式显示。 选择该角色，然后按 Enter。 

    ![包含角色的实体的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    选择正确的实体后，请完成模式输入，然后按 Enter。 模式输入完成后，请[培训](luis-how-to-train.md)应用。

    ![包含两种实体类型的已输入模式的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

上面的模式列表的上下文工具栏中，您可以：

* 搜索模式
* 编辑模式
* 为其他意向重新分配一个模式
* 为其他意向重新分配多个模式
* 删除一个单模式
* 删除多个模式
* 按实体筛选模式列表
* Filter-pattern-list-by-intent
* 删除实体或意向筛选器
* 在意向或实体页上通过现有表述添加模式

## <a name="next-steps"></a>后续步骤

* 了解如何[生成一种模式](luis-tutorial-pattern.md)pattern.any 和教程，说明如何使用角色。
* 了解如何[培训](luis-how-to-train.md)应用。
