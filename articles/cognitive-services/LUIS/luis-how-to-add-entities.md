---
title: 在 LUIS 应用中添加实体 | Microsoft Docs
titleSuffix: Azure
description: 在语言理解 (LUIS) 应用中添加实体（应用程序域中的关键数据）。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: e97f9a5391799849983bd98db5400e0a842627b7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224120"
---
# <a name="manage-entities"></a>管理实体
确定应用的[意向](luis-concept-intent.md)后，需要使用[实体](luis-concept-entity-types.md)[标记示例话语](luis-concept-utterance.md)。 实体是命令或问题的重要部分，对于你的客户端应用执行其任务而言可能是不可或缺的。 

可在应用中通过“实体”页上的“实体列表”来编辑或删除实体。 LUIS 提供两种主要的实体类型：[预生成实体](luis-reference-prebuilt-entities.md)和自己的自定义实体。

以下各部分只能从 LUIS 应用内部的“生成”部分获得。 “生成”链接位于顶部导航栏中。 位于“生成”部分中后，从左侧导航菜单选择“实体”。 将实体添加到应用程序以后，如果实体是机器学习的，可以在话语内[标记实体](luis-how-to-add-example-utterances.md)。 定型并发布应用后，可收到从预测[提取](luis-concept-data-extraction.md)的实体数据。 

## <a name="add-prebuilt-entity"></a>添加预生成实体
在 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 项目中定义预生成实体。 添加到应用程序的常见预生成实体为 number 和 datetimeV2。 

1. 在应用中，从“生成”部分，然后单击左侧面板中的“实体”。
 
2. 在“实体”页上，选择“管理预构建实体”。

    ![在“实体”页上添加预生成实体的屏幕截图](./media/add-entities/manage-prebuilt-entities-button.png)

3. 在“添加或删除预生成实体”对话框中，选择 number 和 datetimeV2 预生成实体。 然后选择“完成”。

    ![“添加预生成实体”对话框屏幕截图](./media/add-entities/list-of-prebuilt-entities.png)

    请参阅[数据提取](luis-concept-data-extraction.md#prebuilt-entity-data)，详细了解如何从终结点 JSON 查询响应提取预生成实体。

## <a name="add-simple-entities"></a>添加简单实体
简单实体是描述单个概念的一般实体。 

1. 在应用中，从“生成”部分，然后单击左侧面板中的“实体”，然后选择“创建新实体”。

    ![“实体”页的屏幕截图，其中已突出显示“创建新实体”按钮](./media/add-entities/create-new-entity-button.png)

2. 在弹出对话框中，在“实体名称”框中键入 `Airline`，从“实体类型”列表选择“简单”，然后选择“完成”。

    ![创建 Airline 简单实体的对话框屏幕截图](./media/add-entities/create-simple-airline-entity.png)

    请参阅[数据提取](luis-concept-data-extraction.md#simple-entity-data)，详细了解如何从终结点 JSON 查询响应提取简单实体。 请尝试简单实体[快速入门](luis-quickstart-primary-and-secondary-data.md)，详细了解如何使用简单实体。

## <a name="add-regular-expression-entities"></a>添加正则表达式实体
基于提供的正则表达式，正则表达式实体用来从话语中拉取数据。 

1. 在应用中，从左侧导航栏选择“实体”，然后选择“创建新实体”。

2. 在弹出对话框中，在“实体名称”框中键入 `AirFrance Flight`，从“实体类型”列表选择“正则表达式”，输入正则表达式 `AFR[0-9]{3,4}`，然后选择“完成”。 

    此 AirFrance Flight 正则表达式需要三个字符（根据字面意思为 `AFR`），后跟 3 或 4 个数字。 数字可以是介于 0 到 9 之间的任何数字。 正则表达式与 AirFrance 航班号相匹配，例如："AFR101"、"ARF1302" 和 "AFR5006"。 请参阅[数据提取](luis-concept-data-extraction.md)，了解有关从终结点 JSON 查询响应中提取实体的详细信息。

    ![创建正则表达式实体的对话框图像](./media/add-entities/regex-entity-create-dialog.png)

    请参阅[数据提取](luis-concept-data-extraction.md#regular-expression-entity-data)，详细了解如何从终结点 JSON 查询响应提取正则表达式实体。 请尝试正则表达式实体[快速入门](luis-quickstart-intents-regex-entity.md)，详细了解如何使用正则表达式实体。

## <a name="add-hierarchical-entities"></a>添加分层实体
分层实体是根据上下文学习的一类概念相关实体。 在以下示例中，该实体包含出发地和目的地。 

在表述 `Book 2 tickets from Seattle to Cairo` 中，Seattle 是出发地，Cairo 是目的地。 每个位置因上下文而异，是从表述中的单词顺序和单词选择学习到的。

若要添加分层实体，请完成以下步骤： 

1. 在应用中，从左侧导航栏选择“实体”，然后选择“创建新实体”。

2. 在弹出对话框中，在“实体名称”框中键入 `Location`，然后从“实体类型”列表选择“分层”。

    ![添加分层的实体](./media/add-entities/hier-location-entity-creation.png)

3. 选择“添加子级”，然后在“子级 #1”框中键入 "Origin"。 

4. 选择“添加子级”，然后在“子级 #2”框中键入 "Destination"。 选择“完成”。
5. 
    >[!NOTE]
    >若要删除子级，请选择它旁边的回收站图标。

    >[!CAUTION]
    >子实体名称在单个应用的所有实体中必须唯一。 两个不同的分层实体不得包含同一名称的子实体。 

    请参阅[数据提取](luis-concept-data-extraction.md#hierarchical-entity-data)，详细了解如何从终结点 JSON 查询响应提取分层实体。 请尝试分层实体[快速入门](luis-quickstart-intent-and-hier-entity.md)，详细了解如何使用分层实体。

## <a name="add-composite-entities"></a>添加复合实体
可以通过创建复合实体定义多个现有实体间的关系。 在以下示例中，该实体包含机票数、出发地和目的地。 

在话语 `Book 2 tickets from Seattle to Cairo` 中，数字 2 与预生成实体相匹配，Seattle 是出发地，Cairo 是目的地。 创建复合实体后，每个实体都是更大父实体的一部分。

1. 在应用中，添加预生成实体 number。 有关说明，请参阅[添加预生成实体](#add-prebuilt-entity)。 

2. 添加分层实体 `Location`，包括子类型：`origin` 和 `destination`。 有关详细说明，请参阅[添加分层实体](#add-hierarchical-entities)。 

3. 从左侧导航栏选择“实体”，然后选择“创建新实体”。

4. 在弹出对话框中，在“实体名称”框中键入 `TicketsOrder`，然后从“实体类型”列表选择“复合”。

5. 选择“添加子级”，添加新的子级。

6. 在“子级 #1”中，从列表选择实体 number。

7. 在“子级 #2”中，从列表选择实体 Location::Origin。 

8. 在“子级 #3”中，从列表选择实体 Location::Destination。 

9. 选择“完成”。

    ![创建复合实体的对话框图像](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >若要删除子级，请选择它旁边的回收站按钮。

    请参阅[数据提取](luis-concept-data-extraction.md#composite-entity-data)，详细了解如何从终结点 JSON 查询响应提取复合实体。 请尝试复合实体[教程](luis-tutorial-composite-entity.md)，详细了解如何使用复合实体。


## <a name="add-patternany-entities"></a>添加 Pattern.any 实体
[Pattern.any](luis-concept-entity-types.md) 仅在 [patterns](luis-how-to-model-intent-pattern.md) 中有效。 此实体有助于 LUIS 查找不同长度和字词选择的实体结尾。 由于此实体会在模式中使用，因此，LUIS 可识别实体结尾在话语中的位置。

如果应用有 `FindBookInfo` 意向，则书名可能会干扰意向预测。 若要清理书名中有哪些字词，请在模式中使用 Pattern.any。 LUIS 预测会从话语开始。 首先，针对实体检查话语并进行匹配，找到实体时，接着检查和匹配模式。 

在话语 `Who wrote the book Ask and when was it published?` 中，书名 Ask 比较棘手，因为标题结束和其他话语开始的位置不是很明显。 书名可以是任意顺序的字词，包括单字、有标点的复杂短语和无意义排序的字词。 通过模式，可以在可提取完整、确切实体的位置创建实体。 找到书名后，预测 `FindBookInfo` 意向，因为这是模式的意向。

1. 在应用中，从“生成”部分，然后单击左侧面板中的“实体”，然后选择“创建新实体”。

2. 在“添加试题”对话框中，在“实体名称”框中键入 `BookTitle`，然后选择“Pattern.any”作为“实体类型”。
 
    ![创建 pattern.any 实体的屏幕截图](./media/add-entities/create-pattern-any-entity.png)

    若要使用 pattern.any 实体，请在“模式”页（在“提升应用性能”部分下）上通过正确的花括号语法（例如“For {BookTitle} who is the author?”）添加模式。

    请参阅[数据提取](luis-concept-data-extraction.md#patternany-entity-data)，详细了解如何从终结点 JSON 查询响应提取 Pattern.any 实体。 请尝试[模式](luis-tutorial-pattern.md)教程，了解如何使用 Pattern.any 实体的详细信息。

如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](luis-concept-patterns.md#explicit-lists)来更正此问题。 

## <a name="add-role-to-pattern-based-entity"></a>将角色添加到基于模式的实体
角色是基于上下文的实体命名子类型。 它相当于[分层](#add-hierarchical-entities)实体，但是角色仅在[模式](luis-how-to-model-intent-pattern.md)中使用。 

例如，机票有“出发城市”和“目的地城市”，但两者都是城市。 LUIS 确定这两者都是城市，并可基于字词顺序和字词选择的上下文确定出发城市和目的地城市。 

角色语法为 {Entity name:Role name}，其中实体名称后面是冒号，然后是角色名称。 例如，“Book a ticket from {Location:Origin} to {Location:Destination}”。

1. 在应用中，从“生成”部分，然后选择左侧面板中的“实体”。

2. 选择“创建新实体”。 输入 `Location` 名称。 选择类型“简单”，然后选择“完成”

3. 从左侧面板选择“实体”，然后选择第 2 步中创建的新实体“Location”。

4. 在“角色名称”文本框中，输入角色 `Origin` 的名称并按 Enter。 添加 `Destination` 的第二个角色名。 例如，机票可以有一个出发城市和一个目的地城市。 这两个角色为 "Origin" 和 "Destination"。

    ![将 Origin 角色添加到 Location 实体的屏幕截图](./media/add-entities/roles-enter-role-name-text.png)

    请参阅[数据提取](luis-concept-data-extraction.md)，详细了解如何从终结点 JSON 查询响应提取角色。 请尝试模式教程，了解如何使用 Pattern.any 实体的详细信息。

## <a name="add-list-entities"></a>添加列表实体
列表实体表示系统中一组固定、封闭的相关单词。 

就饮料列表实体来说，可以有两个规范化的值：water 和 soda pop。 每个规范化的名称都有同义词。 water 的同义词为 H20、gas、flat。 soda pop 的同义词为 fruit、cola、ginger。 创建实体时，不需了解所有值。 可以在查看真实的用户表述后使用同义词添加更多值。

|规范化的名称|同义词|
|--|--|
|Water|H20、gas、flat|
|Soda pop|Fruit、cola、ginger|

1. 在应用中，从“生成”部分，然后单击左侧面板中的“实体”，然后选择“创建新实体”。

2. 在“添加实体”对话框中，在“实体名称”框中键入 `Drinks`，然后选择“列表”作为“试题类型”。 选择“完成”。
 
    ![创建 Drinks 列表实体的对话框图像](./media/add-entities/menu-list-dialog.png)
  
3.  通过此列表实体页可以添加规范化名称。 在“值”文本中，在列表中输入一项，例如在 drinks 列表中输入 `Water`，然后按键盘上的 Enter。 

    ![文本框中有 water 规范化值的 Drinks 列表实体的屏幕截图](./media/add-entities/entity-list-normalized-name.png)

4. 在规范化值 water 的右侧，输入同义的 `h20`、`flat` 和 `gas`，输入每个项后，在键盘上按 Enter。

    ![突出显示 water 同义项的 Drinks 列表实体的屏幕截图](./media/add-entities/menu-list-synonyms.png)

5. 如果想要更多适用于此列表的规范化项，请选择“建议”，查看[语义字典](luis-glossary.md#semantic-dictionary)的选项。

    ![突出显示建议项的 Drink 列表实体的屏幕截图](./media/add-entities/entity-list-recommended-list.png)

6. 在建议列表中选择一项添加为规范化值，或者选择“全部添加”添加所有项。 

    ![突出显示建议项 beer 和“全部添加”按钮的 Drink 列表实体的屏幕截图](./media/add-entities/list-entity-add-suggestions.png)

    请参阅[数据提取](luis-concept-data-extraction.md#list-entity-data)，详细了解如何从终结点 JSON 查询响应提取列表实体。 请尝试[快速入门](luis-quickstart-intent-and-list-entity.md)，详细了解如何使用列表实体。

## <a name="import-list-entity-values"></a>导入列表实体值
可以将值导入现有的列表实体。

 1. 在列表实体页上，选择“导入列表”。

 2. 在“导入新条目”对话框中，选择“选择文件”，然后选择包含该列表的 JSON 文件。

    ![“导入列表实体值”弹出对话框的屏幕截图](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >LUIS 仅导入扩展名为“.json”的文件。

 3. 若要了解有关 JSON 支持的列表语法的信息，请选择“了解有关受支持列表语法的信息”，展开对话框并显示允许的语法示例。 若要折叠对话框并隐藏语法，请再次选择链接标题。

 4. 选择“完成”。

    Colors 列表实体有效 json 的示例显示为以下 JSON 格式的代码：

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>编辑实体名称
1. 在“实体”列表页上，选择列表中的实体。 此操作会转到“实体”页。

2. 在“实体”页上，通过选择实体名称旁的编辑图标编辑实体名称。 实体类型不可编辑。 

## <a name="delete-entity"></a>删除实体

在“实体”页上，选择“删除实体”按钮。 然后，选择确认信息中的“确定”以确认删除。
 
![突出显示“删除实体”按钮的 Location 实体页的屏幕截图](./media/add-entities/entity-delete.png)

>[!NOTE]
>* 删除分层实体会删除其所有子实体。
>* 删除复合实体仅删除复合并中断复合关系，但不会删除构成它的实体。

## <a name="changing-entity-type"></a>更改实体类型
LUIS 不允许更改实体类型，因为它不知道构造该实体要添加或删除的内容。 若要更改类型，最好创建一个名称稍微不同的、类型正确的新实体。 实体创建后，在每句话语中，删除旧标记实体名称并添加新的实体名称。 重新标记所有话语后，即可删除旧实体。 

## <a name="create-a-pattern-from-an-utterance"></a>通过表述创建模式
请参阅[在意向或实体页上通过现有表述添加模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。

## <a name="search-utterances"></a>搜索表述
可使用工具栏的放大镜图标搜索和筛选话语。 

## <a name="train-your-app-after-changing-model-with-entities"></a>在对模型进行实体更改后对应用进行定型
添加、编辑或删除实体后，请针对更改来[定型](luis-how-to-train.md)和[发布](luis-how-to-publish-app.md)应用以影响终结点查询。 

## <a name="next-steps"></a>后续步骤
因为已添加意向、话语和实体，所以有了基本的 LUIS 应用。 了解如何[定型](luis-how-to-train.md)、[测试](luis-interactive-test.md)和[发布](luis-how-to-publish-app.md)应用。
 
