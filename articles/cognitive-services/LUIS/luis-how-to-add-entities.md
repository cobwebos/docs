---
title: Add entities - LUIS
titleSuffix: Azure Cognitive Services
description: Create entities to extract key data from user utterances in Language Understanding (LUIS) apps. Extracted entity data is used by the client application to fullfil customer requests.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383674"
---
# <a name="add-entities-to-extract-data"></a>Add entities to extract data 

Create entities to extract key data from user utterances in Language Understanding (LUIS) apps. Extracted entity data is used by your client application to fullfil customer requests.

实体表示要提取的话语中的字词或短语。 实体描述与意向相关的信息，它们有时对于应用执行任务至关重要。 You can create entities when you add an example utterance to an intent or apart from (before or after) adding an example utterance to an intent.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Plan entities, then create and label

Machine-learned entities can be created from the example utterances or created from the **Entities** page. 

In general, a best practice is to spend time planning the entities before creating a machine-learned entity in the portal. Then create the machine-learned entity from the example utterance with as much detail in the subcomponents and descriptors and constraints as you know at the time. The [decomposable entity tutorial](tutorial-machine-learned-entity.md) demonstrates how to use this method. 

As part of planning the entities, you may know you need text-matching entities (such as prebuilt entities, regular expression entities, or list entities). You can create these from the **Entities** page before they are labeled in example utterances. 

When labeling, you can either label individual entities then build up to a parent machine-learned entity. Or you can start with a parent machine-learned entity and decompose into child entities. 

> [!TIP] 
>Label all words that may indicate an entity, even if the words are not used when extracted in the client application. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Creating an entity before or with labeling

Use the following table to understand which entities where to create or add each entity to the app. 

|实体类型|Where to create entity in the LUIS portal|
|--|--|
|机器学习实体|Entities or Intent detail|
|列表实体|Entities or Intent detail|
|正则表达式实体|实体|
|Pattern.any 实体|实体|
|预生成实体|实体|
|Prebuilt domain entity|实体|

You can create all the entities from the **Entities** page, or you can create a couple of the entities as part of labeling the entity in the example utterance on the **Intent detail** page. You can only _label_ an entity in an example utterance from the **Intent detail** page. 

## <a name="create-a-machine-learned-entity"></a>Create a machine-learned entity

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Create a text-matching entity

Use text-matching entities provide several ways to extract data:

|Text-matching entities|用途|
|--|--|
|[List entity](#add-list-entities-for-exact-matches)|list of canonical names along with synonyms as alternative forms|
|正则表达式实体|match text using a regular expression entity|
|[Prebuilt entity](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|match common data types such as number, email, date|
|Prebuilt domain entity|match using selected subject domains|
|[Pattern.any](#add-a-patternany-entity)| to match entities that may be easily confused with the surrounding text|  

Prebuilt entities work without providing any custom training data. The other entities need you to provide either customer training data (such as List entity's items) or an expression (such as a regular expression or pattern.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>How to create a new custom entity

1. In the LUIS portal, goto the **Manage** section, then the **Entities** page. 
1. Select **+ Create**, then select the entity type. 
1. Continue configuring the entity then select **Create** when you are done. 

### <a name="add-list-entities-for-exact-matches"></a>Add list entities for exact matches

列表实体表示一组固定、封闭的相关单词。 While you, as the author, can change the list, LUIS won't grow or shrink the list. You can also import to an existing list entity using a [list entity .json format(reference-entity-list.md#example-json-to-import-into-list-entity). 

The following list demonstrates the canonical name and the synonyms. 

|Color - list item name|Color - synonyms|
|--|--|
|红色|crimson, blood, apple, fire-engine|
|蓝色|sky, azure, cobalt|
|绿色|kelly, lime|

Use the procedure to create a list entity. Once the list entity is created, you don't need to label example utterances in an intent. List items and synonyms are matched using exact text. 

1. From the **Build** section, select **Entities** in the left panel, and then select **+ Create**.

1. In the **Create an entity type** dialog box, enter the name of the entity, such as `Colors` and select **List**.
1. In the **Create a list entity** dialog box, in the **Add new sublist....** , enter the list item name, such as `Green`, then add synonyms.

    > [!div class="mx-imgBorder"]
    > ![Create a list of colors as a list entity in the Entity detail page.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. When you are finished adding list items and synonyms, select **Create**.

    When you are done with a group of changes to the app, remember to **Train** the app. Do not train the app after a single change. 

    > [!NOTE]
    > This procedure demonstrates creating and labeling a list entity from an example utterance in the **Intent detail** page. You can also create the same entity from the **Entities** page.

## <a name="add-a-role-for-an-entity"></a>Add a role for an entity

A role is a named subtype of an entity, based on context. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Add a role to distinguish different contexts

In the following utterance, there are two locations, and each is specified semantically by the words around it such as `to` and `from`: 

`Pick up the package from Seattle and deliver to New York City.`

In this procedure, add `origin` and `destination` roles to a prebuilt geographyV2 entity.

1. 在“生成”部分的左侧面板中选择“实体”。

1. Select **+ Add prebuilt entity**. Select **geographyV2** then select **Done**. This adds a prebuilt entity to the app.
    
    如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。 

1. Select the newly added prebuilt geographyV2 entity from the **Entities** page list of entities. 
1. To add a new role, select **+** next to **No roles added**.
1. In the **Type role...** textbox, enter the name of the role `Origin` then enter. Add a second role name of `Destination` then enter. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot of adding Origin role to Location entity](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    The role is added to the prebuilt entity but isn't added to any utterances using that entity. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Label text with a role in an example utterance

1. Go to the Intent details page, which has example utterances that use the role. 
1. To label with the role, select the entity label (solid line under text) in the example utterance, then select **View in entity palette** from the drop-down list. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot of selecting View in entity Palette](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    The entity palette opens to the right. 

1. Select the entity, then go to the bottom of the palette and select the role. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot of selecting View in entity Palette](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Add a pattern.any entity

[Pattern.any](luis-concept-entity-types.md) entities are only valid in [patterns](luis-how-to-model-intent-pattern.md), not intents' example utterances. 此类实体有助于 LUIS 查找不同长度和字词选择的实体结尾。 由于此实体会在模式中使用，因此，LUIS 可识别实体末尾在话语模板中的位置。

### <a name="steps-to-create-a-patternany-entity"></a>Steps to create a pattern.any entity

1. From the **Build** section, select **Entities** in the left panel, and then select **+ Create**.

1. In the **Choose an entity type** dialog box, enter the entity name in the **Name** box, and select **Pattern.Any** as the **Type** then select **Create**.

    Once you [create a pattern utterance](luis-how-to-model-intent-pattern.md) using this entity, the entity is extracted with a combined machine-learned and text-matching algorithm. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Create a pattern template utterance to use pattern.any entity

若要使用 pattern.any 实体，请在“模式”页的“提升应用性能”部分中，通过正确的大括号语法（例如 `Where is **{HumanResourcesFormTitle}** on the server?`）添加模式。

如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。 

## <a name="do-not-change-entity-type"></a>Do not change entity type

LUIS 不允许更改实体类型，因为它不知道构造该实体要添加或删除的内容。 若要更改类型，最好创建一个名称稍微不同的、类型正确的新实体。 实体创建后，在每句话语中，删除旧标记实体名称并添加新的实体名称。 重新标记所有话语后，即可删除旧实体。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [Use prebuilt models](howto-add-prebuilt-models.md) 

了解有关以下方面的详细信息：
* How to [train](luis-how-to-train.md)
* How to [test](luis-interactive-test.md)
* How to [publish](luis-how-to-publish-app.md)
* Patterns:
    * [概念](luis-concept-patterns.md)
    * [语法](reference-pattern-syntax.md)
* [Prebuilt entities GitHub repository](https://github.com/Microsoft/Recognizers-Text)
* [Data Extraction concepts](luis-concept-data-extraction.md)


 
