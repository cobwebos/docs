---
title: 使用模式改进 LUIS 预测的教程 - Azure | Microsoft Docs
titleSuffix: Azure
description: 本教程介绍如何使用意向模式来改进 LUIS 意向和实体预测。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265310"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>教程：使用模式来改进预测

本教程介绍如何使用模式来改进意向和实体预测。  

> [!div class="checklist"]
* 如何识别模式是否对应用有帮助
* 如何创建模式 
* 如何在模式中使用预生成实体和自定义实体 
* 如何验证模式预测改进
* 如何向实体添加角色以查找基于上下文的实体
* 如何添加 Pattern.any 来查找自由格式实体

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="import-humanresources-app"></a>导入 HumanResources 应用
本教程中导入了 HumanResources 应用。 该应用具有三个意向：None、GetEmployeeOrgChart、GetEmployeeBenefits。 该应用具有两个实体：预生成数量和员工。 员工实体是提取员工姓名的简单实体。 

1. 创建一个新 LUIS 应用文件，并将其命名为 `HumanResources.json`。 

2. 将以下应用定义复制到文件中：

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. 在 LUIS“应用”页上，选择“导入新应用”。 

4. 在“导入新应用”对话框中，选择在步骤 1 中创建的 `HumanResources.json` 文件。

5. 选择“GetEmployeeOrgChart”意向，然后将“实体视图”更改为“令牌视图”。 列出了几个示例陈述。 每个陈述包含一个姓名，即员工实体。 请注意，每个姓名各不不同，并且每个陈述所使用措辞的排列方式也各不相同。 这种多样性有助于 LUIS 了解各种陈述。

    ![已切换“实体”视图的意向页的屏幕截图](media/luis-tutorial-pattern/utterances-token-view.png)

6. 选择顶部导航栏的“训练”来训练该应用。 等待绿色成功栏出现。

7. 选择顶部面板中的“测试”。 输入 `Who does Patti Owens report to?`，然后选择 Enter。 选择陈述下的“检查”，查看有关测试的详细信息。
    
    员工姓名 Patti Owens 尚未用于示例陈述中。 此测试用于查看 LUIS 对 `GetEmployeeOrgChart` 意向的陈述的理解程度，员工实体应为 `Patti Owens`。 对于 `GetEmployeeOrgChart` 意向，结果应低于 50% (0.50)。 虽然意向是正确的，但得分很低。 员工实体也正确标识为 `Patti Owens`。 模式可提升这一初始预测评分。 

    ![“测试”面板的屏幕截图](media/luis-tutorial-pattern/original-test.png)

8. 选择顶部导航栏上的“测试”按钮，关闭测试面板。 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>模式通过较少实例对 LUIS 进行常见陈述的训练
由于人力资源域的性质，可通过几种常见方式询问组织中的员工关系。 例如：

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

在未提供许多陈述示例的情况下，这些陈述过于相近而无法确定每个陈述的上下文唯一性。 通过为意向添加模式，LUIS 可了解意向的常见陈述模式，而无需提供许多陈述示例。 

此意向的示例模板陈述包括：

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

模式是正则表达式匹配和机器学习的组合。 接下来，提供一些模板陈述示例，以便 LUIS 了解模式。 这些示例和意向陈述使 LUIS 能够更好地理解哪些陈述符合意向，以及陈述中实体的所在位置。 <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>添加模板陈述

1. 在左侧导航栏中，在“提高应用性能”下，从左侧导航栏中选择“模式”。

2. 选择“GetEmployeeOrgChart”意向，然后一次输入一个输入以下模板陈述，在输入每个模板陈述后选择 Enter：

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    `{Employee}` 语法可标记模板陈述中的实体位置以及它是哪个实体。 

    ![为意向输入模板陈述的屏幕截图](./media/luis-tutorial-pattern/enter-pattern.png)

3. 选择顶部导航栏的“训练”。 等待绿色成功栏出现。

4. 选择顶部面板中的“测试”。 在文本框中输入 `Who does Patti Owens report to?`。 选择 Enter。 这是上一节中测试的同一陈述。 对于 `GetEmployeeOrgChart` 意向，结果应该更高。 

    现在的得分好得多。 LUIS 在没有提供许多示例的情况下了解了与意向相关的模式。

    ![得分高的“测试”面板的屏幕截图](./media/luis-tutorial-pattern/high-score.png)

    首先找到实体，然后找到模式，并指示意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。 

5. 选择顶部导航栏上的“测试”按钮，关闭测试面板。

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>使用模式中具有角色的实体
LUIS 应用可用于将员工从一个位置移动到另一个位置。 示例陈述是 `Move Bob Jones from Seattle to Los Colinas`。 陈述中每个位置都具有不同的含义。 Seattle 是原始位置，而 Los Colinas 是要移动的目标位置。 为了区分模式中的这些位置，以下各节中会为具有两个角色（origin 和 destination）的位置创建一个简单实体。 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>创建移动人员和资产的新意向
为任何有关移动人员或资产的陈述创建新意向。

1. 从左侧导航栏中选择“意向”
2. 选择“创建新意向”
3. 将新意向命名为 `MoveAssetsOrPeople`
4. 添加示例陈述：

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![MoveAssetsOrPeople 意向的示例陈述的屏幕截图](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    示例陈述旨在提供充足的示例。 如果稍后在测试中未检测到位置实体，因而未检测到模式，请回到此步骤，并添加更多示例。 然后再次训练并测试。 

5. 使用员工实体标记示例陈述中的实体，方法是在陈述中先后选择名和姓，然后在列表中选择员工实体。

    ![带有员工实体标记的 MoveAssetsOrPeople 陈述的屏幕截图](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. 在陈述 `move travis hinton from portland to orlando` 中，选择文本 `portland`。 在弹出的对话框中，输入新的实体名称 `Location`，然后选择“创建新实体”。 选择“简单”实体类型，然后选择“完成”。

    ![创建新位置实体的屏幕截图](./media/luis-tutorial-pattern/create-new-location-entity.png)

    标记陈述中位置名称的其余部分。 

    ![已标记所有实体的屏幕截图](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    在前面的图像中，模式的选词和顺序是显而易见的。 如果未曾使用模式，而意向上的陈述具有明显的模式，这表明应使用模式。 

    如果期望多种陈述而不是一种模式，则这些陈述是错误的示例陈述。 在这种情况下，可能需要在术语或词语的选择、陈述长度和实体位置等方面大不相同的陈述。 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>将角色添加到位置实体 
角色仅可用于模式。 将 Origin 和 Destination 的角色添加到位置实体。 

1. 在左侧导航窗格中选择“实体”，然后从实体列表中选择“位置”。

2. 将 `Origin` 和 `Destination` 角色添加到实体。

    ![包含角色的新实体的屏幕截图](./media/luis-tutorial-pattern/location-entity.png)

    MoveAssetsOrPeople 意向页上不会标记角色，因为意向陈述上不存在角色。 角色仅存在于模式模板陈述上。 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>添加使用位置和目标角色的模板陈述
添加使用新实体的模板陈述。

1. 从左侧导航栏中选择“模式”。

2. 选择“MoveAssetsOrPeople”意向。

3. 使用新实体 `Move {Employee} from {Location:Origin} to {Location:Destination}` 输入新模板陈述。 模板陈述中实体和角色的语法是 `{entity:role}`。

    ![包含角色的新实体的屏幕截图](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. 为新意向、实体和模式训练应用。

### <a name="test-the-new-pattern-for-role-data-extraction"></a>测试新模式以用于角色数据提取
通过测试验证新模式。

1. 选择顶部面板中的“测试”。 
2. 输入陈述 `Move Tammi Carlson from Bellingham to Winthrop`。
3. 在结果下选择“检查”，查看实体和意向的测试结果。

    ![包含角色的新实体的屏幕截图](./media/luis-tutorial-pattern/test-with-roles.png)

    首先找到实体，然后找到模式，并指示意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。 

4. 选择顶部导航栏上的“测试”按钮，关闭测试面板。

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>使用 Pattern.any 实体模式查找模式中的自由格式实体
此 HumanResources 应用还有助于员工查找公司表单。 许多表单的标题长度各不相同。 可变长度包括一些短语，这些短语可能会在表单名称结束位置方面让 LUIS 受到扰乱。 在模式中使用 Pattern.any 实体，可指定表单名称的开头和结尾，以便 LUIS 正确提取表单名称。 

### <a name="create-a-new-intent-for-the-form"></a>创建表单的新意向
为要查找表单的陈述创建新意向。

1. 从左侧导航栏中选择“意向”。

2. 选择“创建新意向”。

3. 将新意向命名为 `FindForm`。

4. 添加示例陈述。

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![包含角色的新实体的屏幕截图](./media/luis-tutorial-pattern/intent-findform.png)

    表单标题为 `What to do when a fire breaks out in the Lab`。 陈述要求提供表单位置，还要求需签名的人员以便验证员工读取该表单。 如果没有 Pattern.any 实体，则很难理解表单标题的结束位置，也很难将表单标题提取为陈述的实体。

### <a name="create-a-patternany-entity-for-the-form-title"></a>为表单标题创建 Pattern.any 实体
Pattern.any 实体允许使用不同长度的实体。 它仅适用于模式，因为模式会标记实体的开头和结尾。 如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](luis-concept-patterns.md#explicit-lists)来更正此问题。 

1. 在左侧导航栏中选择“意向”。

2. 选择“创建新实体”。 

3. 使用 Pattern.any 类型命名实体 `FormName`。 对于本特定教程，无需向实体添加任何角色。

    ![显示实体名称和实体类型对话框的图像](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>添加使用 Pattern.any 的模式

1. 从左侧导航栏中选择“模式”。

2. 选择“FindForm”意向。

3. 使用新实体 `Where is the form {FormName} and who needs to sign it after I read it?` 输入模板陈述

    ![使用 pattern.any 实体的模板陈述的屏幕截图](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. 为新意向、实体和模式训练应用。

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>测试自由格式的数据提取的新模式
1. 从顶栏中选择“测试”，打开测试面板。 

2. 输入陈述 `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`。

3. 在结果下选择“检查”，查看实体和意向的测试结果。

    ![使用 pattern.any 实体的模板陈述的屏幕截图](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    首先找到实体，然后找到模式，并指示意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。

4. 选择顶部导航栏上的“测试”按钮，关闭测试面板。

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用短语列表改进预测](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions