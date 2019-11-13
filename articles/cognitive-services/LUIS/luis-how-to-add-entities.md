---
title: 添加实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 创建实体，以便从语言理解 (LUIS) 应用的用户话语中提取关键数据。 提取的实体数据由客户端应用程序用来 fullfil 客户请求。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: ed100c27d482065e244bb3dc2cca3b66dfc11986
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013480"
---
# <a name="add-entities-to-extract-data"></a>添加实体以提取数据 

创建实体，以便从语言理解 (LUIS) 应用的用户话语中提取关键数据。 你的客户端应用程序使用提取的实体数据来 fullfil 客户请求。

实体表示要提取的话语中的字词或短语。 实体描述与意向相关的信息，它们有时对于应用执行任务至关重要。 在将示例查询文本添加到意向或从（之前或之后）将示例查询文本添加到目的时，可以创建实体。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="creating-an-entity-is-different-from-labeling-an-entity"></a>创建实体不同于标记实体

首先需要创建实体，然后才能标记示例查询文本中的实体。 

使用下表来了解将每个实体创建或添加到应用中的实体。 

|实体类型|在 LUIS 门户中的何处创建实体|
|--|--|
|计算机学习的实体|实体或意向详细信息|
|列表实体|实体或意向详细信息|
|正则表达式实体|实体|
|Pattern.any 实体|实体|
|预生成实体|实体|
|预生成的域实体|实体|

您可以从 "**实体**" 页创建所有实体，也可以在 "**目的详细信息**" 页上的示例查询文本中，创建一些实体作为标签实体的一部分。 您只能在 "**目的详细信息**" 页的示例查询文本中_标记_实体。 

## <a name="create-a-machine-learned-entity"></a>创建机器学习的实体

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>创建文本匹配实体

使用文本匹配实体提供多种方法来提取数据：

|文本匹配实体|目的|
|--|--|
|[列出实体](#add-list-entities-for-exact-matches)|规范名称和同义词作为替代形式的列表|
|正则表达式实体|使用正则表达式实体匹配文本|
|[预生成实体](tutorial-machine-learned-entity.md#add-prebuilt-number-to-app-to-help-extract-data)|匹配常见数据类型，如数字、电子邮件、日期|
|预生成的域实体|使用选定的使用者域匹配|
|[Pattern.any](#add-a-patternany-entity)| 匹配可能与周围文本容易混淆的实体|  

预生成的实体无需提供任何自定义训练数据即可工作。 其他实体需要你提供客户定型数据（如列出实体的项）或表达式（如正则表达式或模式）。

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>如何创建新的自定义实体

1. 在 LUIS 门户中，依次转到 "**管理**" 部分、"**实体**" 页。 
1. 选择 " **+ 创建**"，然后选择实体类型。 
1. 继续配置实体，并在完成后选择 "**创建**"。 

### <a name="add-list-entities-for-exact-matches"></a>添加列表实体以进行完全匹配

列表实体表示一组固定、封闭的相关单词。 当您作为作者，可以更改列表时，LUIS 不会扩大或缩小列表。 你还可以使用 [列出实体 json 格式（例如，json 到列表实体）导入到现有的列表实体中的一个实体。 

下面的列表演示规范名称和同义词。 

|颜色列表项名称|颜色-同义词|
|--|--|
|红色|crimson、血、apple、消防引擎|
|蓝色|天空，azure，钴|
|绿色|王，酸橙色|

使用过程来创建一个列表实体。 创建列表实体后，不需要在意向中标记示例最谈话。 使用精确文本匹配列表项和同义词。 

1. 从 "**生成**" 部分的左侧面板中选择 "**实体**"，然后选择 " **+ 创建**"。

1. 在 "**创建实体类型**" 对话框中，输入实体的名称，如 "`Colors`" 和 "选择**列表**"。
1. 在 "**创建列表实体**" 对话框中的 "**添加新**子列表 ..." 中，输入列表项名称，如 `Green`，然后添加同义词。

    > [!div class="mx-imgBorder"]
    > ![在实体详细信息页中创建一个颜色列表作为列表实体。](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. 添加完列表项和同义词后，选择 "**创建**"。

    对应用进行一组更改后，请记住**训练**应用。 请不要在一次更改后对应用进行训练。 

    > [!NOTE]
    > 此过程演示如何从 "**意向详细信息**" 页中的示例查询文本创建并标记列表实体。 你还可以从 "**实体**" 页创建相同的实体。

## <a name="add-a-role-for-an-entity"></a>为实体添加角色

角色是基于上下文的实体的命名子类型。 

### <a name="add-a-role-to-distinguish-different-contexts"></a>添加角色以区分不同的上下文

在下面的查询文本中，有两个位置，每个位置都在语义上由围绕它的单词（如 `to` 和 `from`）指定： 

`Pick up the package from Seattle and deliver to New York City.`

在此过程中，将 `origin` 和 `destination` 角色添加到预生成的 geographyV2 实体。

1. 在“生成”部分的左侧面板中选择“实体”。

1. 选择 " **+ 添加预生成实体**"。 选择**geographyV2** ，然后选择 "**完成**"。 这会将预生成的实体添加到应用。
    
    如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。 

1. 从实体的 "**实体**" 页列表中选择新添加的 "预生成的 geographyV2" 实体。 
1. 若要添加新角色，请选择 "**未添加任何角色** **+** " 旁边的 "添加"。
1. 在 "**类型角色 ...** " 文本框中，输入角色的名称，`Origin` 输入。 添加第二个角色名称，然后输入 `Destination`。 

    > [!div class="mx-imgBorder"]
    > 向 Location 实体添加源角色 ![屏幕截图](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    该角色将添加到预生成的实体中，但不会添加到使用该实体的任何最谈话。 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>使用查询文本中的角色标签文本

1. 请参阅意向详细信息页，其中包含使用角色的示例最谈话。 
1. 若要用角色标记，请选择示例查询文本中的实体标签（文本下的实线），然后从下拉列表中选择 "从**实体调色板查看**"。 

    > [!div class="mx-imgBorder"]
    > ![在实体调色板中选择视图的屏幕截图](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    将在右侧打开实体调色板。 

1. 选择实体，然后前往调色板底部，选择 "角色"。 

    > [!div class="mx-imgBorder"]
    > ![在实体调色板中选择视图的屏幕截图](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>添加 pattern.any 实体

[模式。任何](luis-concept-entity-types.md)实体仅适用于[模式](luis-how-to-model-intent-pattern.md)，而不是意向的示例最谈话。 此类实体有助于 LUIS 查找不同长度和字词选择的实体结尾。 由于此实体会在模式中使用，因此，LUIS 可识别实体末尾在话语模板中的位置。

### <a name="steps-to-create-a-patternany-entity"></a>创建模式的步骤。任何实体

1. 从 "**生成**" 部分的左侧面板中选择 "**实体**"，然后选择 " **+ 创建**"。

1. 在 "**选择实体类型**" 对话框中，在 "**名称**" 框中输入实体名称，然后选择 "**模式"。任何**作为**类型**，然后选择 "**创建**"。

    使用此实体[创建模式查询文本](luis-how-to-model-intent-pattern.md)后，将使用组合的计算机学习和文本匹配算法提取实体。 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>创建模式模板查询文本，以使用模式。任何实体

若要使用 pattern.any 实体，请在“模式”页的“提升应用性能”部分中，通过正确的大括号语法（例如 **）添加模式**`Where is **{HumanResourcesFormTitle}** on the server?`。

如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](reference-pattern-syntax.md#explicit-lists)来更正此问题。 

## <a name="do-not-change-entity-type"></a>不要更改实体类型

LUIS 不允许更改实体类型，因为它不知道构造该实体要添加或删除的内容。 若要更改类型，最好创建一个名称稍微不同的、类型正确的新实体。 实体创建后，在每句话语中，删除旧标记实体名称并添加新的实体名称。 重新标记所有话语后，即可删除旧实体。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>后续步骤

详细了解模式：

* [模式概念](luis-concept-patterns.md)
* [模式语法](reference-pattern-syntax.md)

有关预生成实体的详细信息，请参阅 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 项目。 

有关实体在 JSON 终结点查询响应中的显示方式的信息，请参阅[数据提取](luis-concept-data-extraction.md)

因为已添加意向、话语和实体，所以有了基本的 LUIS 应用。 了解如何[定型](luis-how-to-train.md)、[测试](luis-interactive-test.md)和[发布](luis-how-to-publish-app.md)应用。
 
