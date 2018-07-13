---
title: 在 LUIS 应用中添加示例表述 | Microsoft Docs
titleSuffix: Azure
description: 了解如何在语言理解 (LUIS) 应用程序中添加表述。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 74a4b77bd9823e5462eecd438cf4c1d863e79892
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300632"
---
# <a name="add-example-utterances-and-label-with-entities"></a>添加示例表述并通过实体进行标记

示例表述是用户问题或命令的文本示例。 若要教授语言理解 (LUIS)，需将[示例表述](luis-concept-utterance.md)添加到[意向](luis-concept-intent.md)。

通常先将示例表述添加到意向，然后创建实体并在意向页上标记表述。 若要先创建实体，请参阅[添加实体](luis-how-to-add-entities.md)。

## <a name="add-an-utterance"></a>添加表述
在意向页的意向名称下的文本框中输入一个相关的示例表述，例如 `book 2 adult business tickets to Paris tomorrow on Air France`（这是你预期用户会输入的），然后按 Enter。 
 
>[!NOTE]
>LUIS 会将所有表述转换为小写。

![意向详细信息页的屏幕截图，其中突出显示了表述](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

表述会添加到当前意向的表述列表中。 

## <a name="ignoring-words-and-punctuation"></a>忽略单词和标点
若要忽略示例表述中的特定单词或标点，请将 [pattern](luis-concept-patterns.md#pattern-syntax) 与 _ignore_ 语法配合使用。 

## <a name="add-simple-entity-label"></a>添加简单的实体标签
在以下过程中，请在意向页的以下表述中创建并标记自定义实体：

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. 在表述中选择“Air France”，将其标记为简单实体。

    > [!NOTE]
    > 选择要标记为实体的单词时：
    > * 如果是单个单词，请直接将其选中。 
    > * 如果是至少包含两个单词的组合，请先选择组合的开头，然后选择其结尾。

2. 在显示的实体下拉框中，可以选择现有的实体，也可以添加新的实体。 若要添加新的实体，请在文本框中键入其名称，然后选择“创建新实体”。 
 
    ![意向详细信息页的屏幕截图，其中突出显示了简单实体标记选项](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. 在“需要创建什么类型的实体?”弹出对话框中，验证实体名称并选择简单实体类型，然后选择“完成”。

    ![确认对话框的图像](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    请参阅[数据提取](luis-concept-data-extraction.md#simple-entity-data)，详细了解如何从终结点 JSON 查询响应提取简单实体。 请尝试简单实体[快速入门](luis-quickstart-primary-and-secondary-data.md)，详细了解如何使用简单实体。


## <a name="add-list-entity-and-label"></a>添加列表实体和标签
列表实体表示系统中一组固定、封闭（文本完全匹配）的相关单词。 

就饮料列表实体来说，可以有两个规范化的值：water 和 soda pop。 每个规范化的名称都有同义词。 water 的同义词为 H20、gas、flat。 soda pop 的同义词为 fruit、cola、ginger。 创建实体时，不需了解所有值。 可以在查看真实的用户表述后使用同义词添加更多值。

|规范化的名称|同义词|
|--|--|
|Water|H20、gas、flat|
|Soda pop|Fruit、cola、ginger|

从意向页创建新的列表实体时，你在做两件看起来可能并不明显的事情。 第一件事，通过添加第一个列表项来创建新的列表。 第二件事，使用从表述中选择的单词或短语为第一个列表项命名。 虽然可以稍后在实体页中更改这些，但就列表项的名称来说，选择有所需单词的表述的速度会更快。

例如，如果需要创建一个包含饮料类型的列表，而你从表述中选择了单词 `h2o` 来创建实体，则列表会有一个项，其名称为 h20。 如果需要更通用的名称，则应选择使用更通用名称的表述。 

1. 在表述中选择属于列表中的第一个项的单词，然后在文本框中输入列表的名称，再选择“创建新实体”。   

    ![“意向详细信息”页的屏幕截图，其中已突出显示“创建新实体”](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. 在“需要创建什么类型的实体?”对话框中，添加此列表项的同义词。 对于饮料列表中的水项，请添加 `h20`、`perrier` 和 `waters`，然后选择“完成”。 请注意，之所以添加“waters”，是因为列表同义词在令牌级别匹配。 在英语文化中，该级别属于单词级别，因此“waters”与“water”并不匹配，但在列表中除外。 

    ![“需要创建什么类型的实体”对话框的屏幕截图](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    此饮料列表只有一个饮料类型：水。 若要添加更多饮料类型，可以标记其他表述，也可以在左侧导航区域的“实体”中编辑实体。 [编辑](luis-how-to-add-entities.md#add-list-entities)实体时，可以选择输入其他具有相应同义词的项，也可以选择[导入](luis-how-to-add-entities.md#import-list-entity-values)一个列表。 

    请参阅[数据提取](luis-concept-data-extraction.md#list-entity-data)，详细了解如何从终结点 JSON 查询响应提取列表实体。 请尝试[快速入门](luis-quickstart-intent-and-list-entity.md)，详细了解如何使用列表实体。

## <a name="add-synonyms-to-the-list-entity"></a>向列表实体添加同义词 
通过在表述中选择单词或短语，向列表实体添加同义词。 如果有一个“饮料”列表实体，需要添加 `agua` 作为水的同义词，请执行以下步骤：

在表述中选择同义的单词，例如水的同义词 `aqua`，然后在下拉列表中选择列表实体名称，例如“饮料”，接着选择“设置为同义词”，再选择与之同义的列表项，例如 **water**。

![“意向详细信息”页的屏幕截图，其中已突出显示“创建新同义词”](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>创建列表实体的新项
通过在表述中选择单词或短语，创建现有列表实体的新项。 如果有一个“饮料”列表，需要添加 `tea` 作为新项，请执行以下步骤：

在表述中选择用作新列表项的单词，例如 `tea`，然后在下拉列表中选择列表实体名称，例如“饮料”，接着选择“创建新同义词”。 

![添加新列表项操作的屏幕截图](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

该单词现在突出显示为蓝色。 如果将光标悬停在单词上方，则会显示一个表明列表项名称的标记，例如“茶”。

![新列表项标记的屏幕截图](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>将实体包装进复合标签中
复合实体是从**实体**创建的。 不能从“意向”页创建复合实体。 创建复合实体以后，即可将实体包装在“意向”页的表述中。 

假定表述为 `book 2 tickets from Seattle to Cairo`，则一个复合表述可以在单个父实体中返回下述实体信息：票数 (2)、出发地 (Seattle)、目的地 (Cairo)。 

请按这些[步骤](luis-how-to-add-entities.md#add-prebuilt-entity)添加“数字”预置实体。 创建此实体以后，表述中的 `2` 为蓝色，表示它是标记的实体。 预置实体由 LUIS 标记。 不能在单一表述中添加或删除预置实体标签。 只能通过在应用程序中添加或删除预置实体的方式添加或删除所有预置标签。

按这些[步骤](#add-hierarchical-entity-and-label)创建“位置”分层实体。 在示例表述中标记出发地和目的地。 

在复合实体中包装实体之前，请确保所有子实体都突出显示为蓝色，意味着它们已在表述中进行标记。

1. 若要将单个实体包装到复合实体中，请在复合实体的表述中选择第一个标记的实体。 在示例表述 `book 2 tickets from Seattle to Cairo` 中，第一个实体为数字 2。 此时会出现一个下拉列表，显示该选择操作所做的选择。

    ![所选数字以及突出显示的下拉列表选项的屏幕截图](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. 从下拉列表中选择“包装复合实体”。 

    ![用于包装复合实体的下拉列表选项的屏幕截图，其中突出显示了“在复合实体中进行包装”](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. 选择复合实体的最后一个单词。 在此示例的表述中，选择“Location::Destination”（代表 Cairo）。 现在会在属于复合实体的表述的所有单词（包括非实体单词）下显示绿线。

    ![BookFlight“意向”页的屏幕截图，突出显示了数字](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. 从下拉列表中选择复合实体名称。 就本示例来说，该名称为 **TicketOrder**。

    ![使用复合实体包装单词的屏幕截图，在下拉列表中突出显示了复合实体名称](./media/luis-how-to-add-example-utterances/wrap-4.png)

    正确包装实体后，会在整个短语下显示绿线。

    ![突出显示了复合实体的表述的屏幕截图](./media/luis-how-to-add-example-utterances/wrap-5.png)

    请参阅[数据提取](luis-concept-data-extraction.md#composite-entity-data)，详细了解如何从终结点 JSON 查询响应提取复合实体。 请尝试复合实体[教程](luis-tutorial-composite-entity.md)，详细了解如何使用复合实体。

## <a name="add-hierarchical-entity-and-label"></a>添加分层实体和标签
分层实体是根据上下文学习的一类概念相关实体。 在以下示例中，该实体包含出发地和目的地。 

在表述 `Book 2 tickets from Seattle to Cairo` 中，Seattle 是出发地，Cairo 是目的地。 每个位置因上下文而异，是从表述中的单词顺序和单词选择学习到的。

1. 在“意向”页的表述中选择“Seattle”，然后输入实体名称“位置”，再选择“创建新实体”。

    ![“创建分层实体标签”对话框的屏幕截图](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. 在弹出对话框中，选择“分层”作为“实体类型”，然后添加 `Origin` 和 `Destination` 作为子项，接着选择“完成”。

    ![“意向详细信息”页的屏幕截图，突出显示了“ToLocation”实体](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. 表述中的单词已用父分层实体标记。 需将单词分配给子实体。 返回到意向页上的表述。 选择单词，然后从下拉列表中选择所创建的实体名称，再按照右侧菜单的提示选择正确的子实体。

    ![“意向详细信息”页的屏幕截图，突出显示了“ToLocation”实体](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >子实体名称在单个应用的所有实体中必须唯一。 两个不同的分层实体不得包含同一名称的子实体。 

    请参阅[数据提取](luis-concept-data-extraction.md#hierarchical-entity-data)，详细了解如何从终结点 JSON 查询响应提取分层实体。 请尝试分层实体[快速入门](luis-quickstart-intent-and-hier-entity.md)，详细了解如何使用分层实体。


## <a name="remove-entity-labels-from-utterances"></a>从表述中删除实体标签
可以在“意向”页的表述中删除机器学习的实体标签。 如果实体不是机器学习的，则不能将其从表述中删除。 如果需要从表述中删除非机器学习实体，则需从整个应用中删除实体。 

如果需要从表述中删除机器学习实体标签，请在表述中选择实体。 然后，在显示的实体下拉框中选择“删除标签”。

![“意向详细信息”页的屏幕截图，其中突出显示了“删除标签”](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>添加预置实体标签
如果向 LUIS 应用添加预置实体，则不需通过这些实体来标记表述。 若要详细了解预置实体以及添加它们的方法，请参阅[添加实体](luis-how-to-add-entities.md#add-prebuilt-entity)。

## <a name="add-regular-expression-entity-label"></a>添加正则表达式实体标签
如果向 LUIS 应用添加正则表达式实体，则不需通过这些实体来标记表述。 若要详细了解正则表达式实体以及添加它们的方法，请参阅[添加实体](luis-how-to-add-entities.md#add-regular-expression-entities)。

## <a name="create-a-pattern-from-an-utterance"></a>通过表述创建模式
请参阅[在意向或实体页上通过现有表述添加模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。

## <a name="add-patternany-entity-label"></a>添加 pattern.any 实体标签
如果向 LUIS 应用添加 pattern.any 实体，则不能通过这些实体来标记表述。 它们只在模式中有效。 若要详细了解 pattern.any 实体以及添加它们的方法，请参阅[添加实体](luis-how-to-add-entities.md#add-patternany-entities)。

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>通过表述更改模型后对应用进行训练
添加、编辑或删除表述后，请先[训练](luis-how-to-train.md)再[发布](PublishApp.md)应用，这样更改才会影响终结点查询。 

## <a name="next-steps"></a>后续步骤

在意向中标记表述后，就可以创建[复合实体](luis-how-to-add-entities.md)了。
