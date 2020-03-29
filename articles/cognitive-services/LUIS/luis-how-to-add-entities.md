---
title: 添加实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 创建实体，以便从语言理解 (LUIS) 应用的用户话语中提取关键数据。 提取的实体数据由客户端应用程序用于完全删除客户请求。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220904"
---
# <a name="add-entities-to-extract-data"></a>添加实体以提取数据 

创建实体，以便从语言理解 (LUIS) 应用的用户话语中提取关键数据。 提取的实体数据由客户端应用程序用于完全删除客户请求。

实体表示要提取的话语中的字词或短语。 实体描述与意向相关的信息，它们有时对于应用执行任务至关重要。 当您向意图添加示例陈述时，可以在将示例陈述添加到意图之外，或者（在之前或之后）将示例陈述添加到意图之外，从而创建实体。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>规划实体，然后创建和标记

机器学习的实体可以从示例陈述创建，也可以从 **"实体"** 页创建。 

通常，最佳做法是在门户中创建机器学习实体之前花时间规划实体。 然后，从示例陈述中创建机器学习的实体，在子组件、描述符和约束中具有与当时所知的一样多的详细信息。 [可分解实体教程](tutorial-machine-learned-entity.md)演示如何使用此方法。 

作为规划实体的一部分，您可能知道需要文本匹配实体（如预构建实体、正则表达式实体或列表实体）。 您可以在 **"实体"** 页创建这些，然后再在示例陈述中标记它们。 

标记时，可以标记单个实体，然后构建到父计算机学习实体。 或者，可以从父计算机学习实体开始，然后分解为子实体。 

> [!TIP] 
>标记可能指示实体的所有单词，即使这些单词在客户端应用程序中提取时未使用也是如此。 

## <a name="creating-an-entity-before-or-with-labeling"></a>在标记之前创建实体

使用下表了解要创建每个实体或将每个实体添加到应用的实体。 

|实体类型|在 LUIS 门户中创建实体的位置|
|--|--|
|机器学习实体|实体或意向详细信息|
|列表实体|实体或意向详细信息|
|正则表达式实体|实体|
|Pattern.any 实体|实体|
|预生成实体|实体|
|预构建的域实体|实体|

可以从 **"实体"** 页创建所有实体，也可以创建几个实体，作为**在"意向详细信息**"页上的示例陈述中标记实体的一部分。 _您只能在_**"意向"详细信息**页中标记示例陈述中的实体。 

## <a name="create-a-machine-learned-entity"></a>创建机器学习实体

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>创建文本匹配实体

使用文本匹配实体提供了几种提取数据的方法：

|文本匹配实体|目的|
|--|--|
|[列表实体](#add-list-entities-for-exact-matches)|规范名称列表以及同义词作为替代形式|
|正则表达式实体|使用正则表达式实体匹配文本|
|[预生成实体](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|匹配常见数据类型，如数字、电子邮件、日期|
|预构建的域实体|使用所选主题域匹配|
|[Pattern.any](#add-a-patternany-entity)| 匹配可能很容易与周围文本混淆的实体|  

预构建的实体无需提供任何自定义训练数据即可工作。 其他实体需要您提供客户培训数据（如 List 实体的项目）或表达式（如正则表达式或模式。

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>如何创建新的自定义实体

1. 在 LUIS 门户中，转到 **"管理**"部分，然后转到 **"实体"** 页。 
1. 选择 **= 创建**，然后选择实体类型。 
1. 继续配置实体，然后选择完成后**选择"创建**"。 

### <a name="add-list-entities-for-exact-matches"></a>添加列表实体以进行完全匹配

列表实体表示一组固定、封闭的相关单词。 虽然您作为作者可以更改列表，但 LUIS 不会增长或缩小列表。 您还可以使用 _list 实体 .json 格式（引用实体列表.md_示例-json-导入到列表实体）导入到现有列表实体。 

下面的列表演示了规范名称和同义词。 

|颜色 - 列表项名称|颜色 - 同义词|
|--|--|
|Red|深红， 血液， 苹果， 消防车|
|蓝色|天空， 蔚蓝， 钴|
|绿色|凯莉， 石灰|

使用该过程创建列表实体。 创建列表实体后，无需在意图中标记示例陈述。 列表项和同义词使用精确文本进行匹配。 

1. 从 **"生成"** 部分中，选择左侧面板中的**实体**，然后选择 **" 创建**"。

1. 在"**创建实体类型"** 对话框中，输入实体的名称，如`Colors`和 选择 **"列表**"。
1. 在"**创建列表实体**"对话框中，在 **"添加新子列表..."** 中，输入列表项名称，如`Green`，然后添加同义词。

    > [!div class="mx-imgBorder"]
    > ![在"实体详细信息"页中创建颜色列表作为列表实体。](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. 完成添加列表项和同义词后，选择 **"创建**"。

    完成对应用的一组更改后，请记住**训练**应用。 在一次更改后，不要训练应用。 

    > [!NOTE]
    > 此过程演示了从 **"意向详细信息**"页中的示例陈述创建和标记列表实体。 您还可以从 **"实体"** 页创建相同的实体。

## <a name="add-a-role-for-an-entity"></a>添加实体的角色

角色是基于上下文的实体的命名子类型。 

### <a name="add-a-role-to-distinguish-different-contexts"></a>添加角色以区分不同的上下文

在以下陈述中，有两个位置，每个位置都由其周围的单词（如`to`和`from`） 在语义上指定： 

`Pick up the package from Seattle and deliver to New York City.`

在此过程中，向预构建`origin`的`destination`地理V2实体添加和角色。

1. 在“生成”部分的左侧面板中选择“实体”********。

1. 选择 **= 添加预构建的实体**。 选择**地理V2，** 然后选择 **"完成**"。 这会向应用添加预构建的实体。
    
    如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。 

1. 从实体的 **"实体"** 页面列表中选择新添加的预构建地理 V2 实体。 
1. 要添加新角色，请选择**+****"未添加"角色**旁边。
1. 在 **"类型角色..."** 文本框中，输入角色`Origin`的名称，然后输入。 添加`Destination`输入的第二个角色名称。 

    > [!div class="mx-imgBorder"]
    > ![将 Origin 角色添加到 Location 实体的屏幕截图](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    角色将添加到预构建的实体中，但不会添加到使用该实体的任何陈述中。 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>在示例陈述中具有角色的标签文本

1. 转到"意向详细信息"页，该页面包含使用该角色的示例陈述。 
1. 要使用角色进行标注，请在示例陈述中选择实体标签（文本下的实线），然后从下拉列表中选择 **"在实体调色板中查看**"。 

    > [!div class="mx-imgBorder"]
    > ![选择实体调色板中的视图的屏幕截图](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    实体调色板向右打开。 

1. 选择实体，然后转到调色板的底部并选择角色。 

    > [!div class="mx-imgBorder"]
    > ![选择实体调色板中的视图的屏幕截图](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>添加 pattern.any 实体

[模式.任何](luis-concept-entity-types.md)实体仅在[模式](luis-how-to-model-intent-pattern.md)中有效，而不是意图的示例陈述。 此类实体有助于 LUIS 查找不同长度和字词选择的实体结尾。 由于此实体会在模式中使用，因此，LUIS 可识别实体末尾在话语模板中的位置。

### <a name="steps-to-create-a-patternany-entity"></a>创建模式的步骤。

1. 从 **"生成"** 部分中，选择左侧面板中的**实体**，然后选择 **" 创建**"。

1. 在"**选择实体类型"** 对话框中，在 **"名称"** 框中输入实体名称，然后选择 **"模式"。** **Type** **Create**

    使用此实体[创建模式陈述](luis-how-to-model-intent-pattern.md)后，将使用计算机学习与文本匹配算法的组合提取实体。 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>创建模式模板陈述以使用模式。

若要使用 pattern.any 实体，请在“模式”页的“提升应用性能”部分中，通过正确的大括号语法（例如 `Where is **{HumanResourcesFormTitle}** on the server?`）添加模式********。

如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。 

## <a name="do-not-change-entity-type"></a>不要更改实体类型

LUIS 不允许更改实体类型，因为它不知道构造该实体要添加或删除的内容。 若要更改类型，最好创建一个名称稍微不同的、类型正确的新实体。 实体创建后，在每句话语中，删除旧标记实体名称并添加新的实体名称。 重新标记所有话语后，即可删除旧实体。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [使用预构建的模型](howto-add-prebuilt-models.md) 

了解有关以下方面的详细信息：
* 如何[训练](luis-how-to-train.md)
* 如何[测试](luis-interactive-test.md)
* 如何[发布](luis-how-to-publish-app.md)
* 模式：
    * [概念](luis-concept-patterns.md)
    * [语法](reference-pattern-syntax.md)
* [预构建实体 GitHub 存储库](https://github.com/Microsoft/Recognizers-Text)
* [数据提取概念](luis-concept-data-extraction.md)


 
