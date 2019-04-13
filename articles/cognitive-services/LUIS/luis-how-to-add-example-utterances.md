---
title: 添加示例话语
titleSuffix: Language Understanding - Azure Cognitive Services
description: 示例表述是用户问题或命令的文本示例。 若要传授语言理解 (LUIS)，需将示例话语添加到意向。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 0d3123b1e0238a1907b5ad3d487b92a7919ff181
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524253"
---
# <a name="add-an-entity-to-example-utterances"></a>将实体添加到示例话语 

示例表述是用户问题或命令的文本示例。 若要教授语言理解 (LUIS)，需将[示例表述](luis-concept-utterance.md)添加到[意向](luis-concept-intent.md)。

通常先将示例话语添加到意向，然后创建实体并在意向页上标记话语。 若要先创建实体，请参阅[添加实体](luis-how-to-add-entities.md)。

## <a name="marking-entities-in-example-utterances"></a>在示例话语中标记实体

在示例话语中选择文本以标记实体时，会就地显示弹出菜单。 使用此菜单可以创建或选择实体。 

无法标记示例话语中的某些实体类型（例如预生成实体和正则表达式实体），因为它们会自动进行标记。 

## <a name="add-a-simple-entity"></a>添加简单实体

在以下过程中，请在意向页的以下话语中创建并标记自定义实体：

```text
Are there any SQL server jobs?
```

1. 在话语中选择 `SQL server`，将其标记为简单实体。 在显示的实体下拉框中，可以选择现有的实体，也可以添加新的实体。 若要添加新实体，请键入其名称`Job`在文本框中，然后选择**创建新实体**。

    ![输入实体名称的屏幕截图](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > 选择要标记为实体的单词时：
    > * 如果是单个单词，请直接将其选中。 
    > * 如果是至少包含两个单词的组合，请先选择组合的开头，然后选择其结尾。

1. 在“需要创建什么类型的实体?”弹出对话框中，验证实体名称并选择简单实体类型，然后选择“完成”。

    [短语列表](luis-concept-feature.md)通常用于提升简单实体的信号。

## <a name="add-a-list-entity"></a>添加列表实体

列表实体在系统中表示一组完全匹配文本的相关字词的匹配项。 

对于公司的部门列表，可以使用规范化值：`Accounting` 和 `Human Resources`。 每个规范化的名称都有同义词。 对于部门，这些同义词可以包含任何部门首字母缩写、编号或俚语。 创建实体时，不需了解所有值。 可以在查看真实的用户表述后使用同义词添加更多值。

1. 在上的示例查询文本**意向**页上，新列表中选择的单词或短语所需。 实体下拉列表出现时，在顶部文本框中，输入新的列表中实体的名称，然后选择**创建新实体**。   

1. 在“要创建哪种类型的实体?”弹出框中，为实体命名并选择“列表”作为类型。 添加此列表项的同义词，然后选择“完成”。 

    ![输入列表实体同义词的屏幕截图](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    若要添加更多的列表项或项同义词，可以标记其他话语，也可以在左侧导航区域的“实体”中编辑实体。 [编辑](luis-how-to-add-entities.md#add-list-entities)实体时，可以选择输入其他具有相应同义词的项，也可以选择导入一个列表。 

## <a name="add-composite-entity"></a>添加复合实体

复合实体是在父实体中基于现有**实体**创建的。 

假设将语音样本`Does John Smith work in Seattle?`，复合语句可以返回实体信息的员工姓名`John Smith`，和位置`Seattle`复合实体中。 子实体必须已存在于应用程序和标记中创建复合实体之前将示例语音样本。

1. 若要将子实体包装到一个复合实体，请选择**第一个**标记 （左） 的复合实体查询文本中的实体。 下拉列表将出现以显示此选择的选项。

1. 选择**将包装在复合实体**从下拉列表。 

1. 选择复合实体的最后一个单词（最右侧）。 请注意，复合实体带有绿线。 这是一个复合实体的可视指示器，应从最左边的子实体到最右边的子实体的复合实体中的所有单词下。

1. 输入下拉列表中的复合实体名称。

    正确包装实体后，会在整个短语下显示绿线。

1. 在“要创建哪种类型的实体?”弹出框中验证复合实体详细信息，然后选择“完成”。

    ![“实体详细信息”弹出窗口的屏幕截图](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. 复合实体将以蓝色突出显示各个实体，而整个复合实体下面带有绿色下划线。 

    ![显示了复合实体的“意向详细信息”页的屏幕截图](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>添加分层的实体

**层次结构的实体最终将被弃用。使用[实体角色](luis-concept-roles.md)以确定实体子类型，而不是分层实体。**

分层实体是根据上下文学习的一类概念相关实体。 在以下示例中，该实体包含出发地和目的地。 

在表述 `Move John Smith from Seattle to Cairo` 中，Seattle 是出发地，Cairo 是目的地。 每个位置因上下文而异，是从表述中的单词顺序和单词选择学习到的。

1. 在“意向”页上的话语中选择 `Seattle`，输入实体名称 `Location`，然后按 Enter 键。

1. 在“要创建哪种类型的实体?”弹出框中，选择“分层”作为“实体类型”，添加 `Origin` 和 `Destination` 作为子项，然后选择“完成”。

    ![“意向详细信息”页的屏幕截图，突出显示了“ToLocation”实体](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. 表述中的单词已用父分层实体标记。 需将单词分配给子实体。 意向的详细信息页上返回到查询文本。 选择单词，然后从下拉列表中选择所创建的实体名称，再按照右侧菜单的提示选择正确的子实体。

    >[!CAUTION]
    >子实体名称在单个应用的所有实体中必须唯一。 两个不同的分层实体不得包含同一名称的子实体。 

## <a name="add-entitys-role-to-utterance"></a>将实体的角色添加到查询文本

角色是命名的实体，由查询文本的上下文的子类型。 可以将标记作为实体，查询文本中的实体或选择该实体中的角色。 任何实体可以具有包括机器学习的内容的自定义实体的角色 （简单实体和复合实体），不是机器学习的内容 （预生成的实体，正则表达式实体列表的实体）。 

了解[如何将标记与实体角色查询文本](tutorial-entity-roles.md)从动手教程。 

## <a name="entity-status-predictions"></a>实体状态预测

在 LUIS 门户中输入新话语时，该话语可能会出现实体预测误差。 预测误差是指实体标记方式与 LUIS 预测实体的方式之间的差异。 

在 LUIS 门户中，将通过为话语添加一个红色下划线来直观显示这种差异。 红色下划线可能出现在实体括号内部或括号外部。 

![实体状态预测差异的屏幕截图](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

在话语中选择带红色下划线的单词。 

实体框将显示“实体状态”，如果出现预测差异，则会显示一个红色感叹号。 若要在“实体状态”中查看有关标记实体与预测实体之间的差异的信息，请选择“实体状态”，然后选择右侧的项。

![屏幕截图的实体状态选择](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

红线可以出现在任何以下时间：

   * 输入查询文本时（但在标记实体之前）
   * 应用实体标签时
   * 删除实体标签时
   * 预测该文本的多个实体标签时 

以下解决方法可帮助解决实体预测差异：

|实体|可视的指示器|预测|解决方案|
|--|--|--|--|
|输入了话语，但尚未标记实体。|红色下划线|预测正确。|标记带有预测值的实体。|
|未标记的文本|红色下划线|错误的预测|需要在所有意向中检查使用此错误实体的当前话语。 当前话语错误地告知 LUIS 此文本是预测的实体。
|正确标记的文本|蓝色实体突出显示，带红色下划线|错误的预测|在各种位置和用法中提供包含正确标记的实体的更多话语。 当前话语不足，无法告知 LUIS 此实体是或者类似于同一上下文中显示的实体。 类似实体应组合成单个实体，以免给 LUIS 造成混淆。 另一种解决方法是添加一个短语列表，以提升单词的重要性。 |
|错误标记的文本|蓝色实体突出显示，带红色下划线|正确的预测| 在各种位置和用法中提供包含正确标记的实体的更多话语。 

> [!Note]
> 红色框后标记为目的的示例查询文本，行中围绕[意向预测误差](luis-how-to-add-intents.md#intent-prediction-discrepancy-errors)已发生。 您需要更正它。 

## <a name="other-actions"></a>其他操作

可以针对示例话语（选定组或单个项的形式）执行操作。 选定的示例话语组会更改列表上方的上下文菜单。 单个项可以使用列表上方的上下文菜单，以及每个话语行末尾的相应上下文省略号。 

### <a name="remove-entity-labels-from-utterances"></a>从表述中删除实体标签

可以在“意向”页的表述中删除机器学习的实体标签。 如果实体不是机器学习的，则不能将其从表述中删除。 如果需要从表述中删除非机器学习实体，则需从整个应用中删除实体。 

如果需要从表述中删除机器学习实体标签，请在表述中选择实体。 然后，在显示的实体下拉框中选择“删除标签”。

### <a name="add-prebuilt-entity-label"></a>添加预置实体标签

向 LUIS 应用添加预生成实体时，无需通过这些实体来标记话语。 若要详细了解预置实体以及添加它们的方法，请参阅[添加实体](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)。

### <a name="add-regular-expression-entity-label"></a>添加正则表达式实体标签

如果向 LUIS 应用添加正则表达式实体，则不需要通过这些实体来标记话语。 若要详细了解正则表达式实体以及添加它们的方法，请参阅[添加实体](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts)。


### <a name="create-a-pattern-from-an-utterance"></a>通过表述创建模式

请参阅[在意向或实体页上通过现有表述添加模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。


### <a name="add-patternany-entity"></a>添加 pattern.any 实体

如果向 LUIS 应用添加 pattern.any 实体，则不能通过这些实体来标记表述。 它们只在模式中有效。 若要详细了解 pattern.any 实体以及添加它们的方法，请参阅[添加实体](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities)。

## <a name="train-your-app-after-changing-model-with-utterances"></a>通过表述更改模型后对应用进行训练

添加、编辑或删除表述后，请先[训练](luis-how-to-train.md)再[发布](luis-how-to-publish-app.md)应用，这样更改才会影响终结点查询。 

## <a name="next-steps"></a>后续步骤

在意向中标记表述后，就可以创建[复合实体](luis-how-to-add-entities.md)了。
