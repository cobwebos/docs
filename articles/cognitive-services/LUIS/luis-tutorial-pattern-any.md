---
title: 教程 5：自由格式文本的 Pattern.any 实体
titleSuffix: Azure Cognitive Services
description: 对于格式良好且数据结尾可能容易与话语的剩余单词混淆的话语，使用 pattern.any 实体可从中提取数据。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 90dc7b8bc69c86128b65c16920886b7c4af5c5cf
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888299"
---
# <a name="tutorial-5-extract-free-form-data"></a>教程 5：提取自由格式数据

在本教程中，对于格式良好且数据结尾可能容易与话语的剩余单词混淆的话语，我们使用 pattern.any 实体从这些话语中提取数据。 

通过 pattern.any 实体，可找到自由格式数据，其中实体的用语方式比较晦涩，难以从话语的其余部分来确定实体的结尾。 

此人力资源应用有助于员工查找公司表单。 

|话语|
|--|
|**HRF-123456** 在哪里？|
|**HRF-123234** 的作者是谁？|
|**HRF-456098** 是以法语发布的吗？|

但是，每个表单都具有一个带格式的名称（如上表所使用的名称），以及一个易记名称，如 `Request relocation from employee new to the company 2018 version 5`。 

具有易记表单名称的话语如下所示：

|话语|
|--|
|“公司新员工请求调职 2018 年第 5 版”在哪里？|
|“公司新员工请求调职 2018 年第 5 版”的作者是谁？|
|“公司新员工请求调职 2018 年第 5 版”是以法语发布的吗？|

此可变长度包含可能导致 LUIS 无法正确识别实体结尾位置的单词。 在模式中使用 Pattern.any 实体，可指定表单名称的开头和结尾，以便 LUIS 正确提取表单名称。

|模板话语示例|
|--|
|{FormName} 在哪里[？]|
|{FormName} 的作者是谁[？]|
|{FormName} 是以法语发布的吗[？]|

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 使用现有的教程应用
> * 将示例话语添加到现有实体
> * 创建 Pattern.any 实体
> * 创建模式
> * 训练
> * 测试新模式

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用
继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `patt-any`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="add-example-utterances"></a>添加示例话语 
如果很难创建和标记 FormName 实体，请删除预生成的 keyPhrase 实体。 

1. 从顶部导航栏中选择“生成”，然后从左侧导航栏中选择“意向”。

2. 从“意向”列表中选择“FindForm”。

3. 添加一些示例话语：

    |示例陈述|
    |--|
    |“实验室着火时该怎么办”的表单在哪里，在我阅读该表单后需要谁签名？|
    |“公司新员工请求调职”在服务器上的哪里？|
    |“主要校园的健康和保健请求”的作者是谁并且最新版本是什么？|
    |我正在查找名为“包括物理资产的 Office 移动请求”的表单。 |

    如果没有 Pattern.any 实体，则 LUIS 很难理解表单标题的结束位置，因为表单名称有很多种变体。

## <a name="create-a-patternany-entity"></a>创建 Pattern.any 实体
Pattern.any 实体提取不同长度的实体。 它仅适用于模式，因为模式会标记实体的开头和结尾。 如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](luis-concept-patterns.md#explicit-lists)来更正此问题。 

1. 在左侧导航栏中选择“意向”。

2. 选择“创建新实体”，输入名称 `FormName`，然后选择“Pattern.any”作为类型。 选择“完成”。 

    由于 Pattern.any 仅在模式中才有效，因此，不能在意向中标记实体。 

    如果想要使提取的数据包含 number 或 datetimeV2 等其他实体，则需要创建包含 Pattern.any、number 和 datetimeV2 的复合实体。

## <a name="add-a-pattern-that-uses-the-patternany"></a>添加使用 Pattern.any 的模式

1. 从左侧导航栏中选择“模式”。

2. 选择“FindForm”意向。

3. 输入以下使用新实体的模板话语：

    |模板话语|
    |--|
    |["]{FormName}["] 表单在哪里，在我阅读该表单后需要谁签名[？]|
    |["]{FormName}["] 在服务器上的哪里[？]|
    |["]{FormName}["] 的作者是谁并且最新版本是什么[？]|
    |我正在查找名为 ["]{FormName}["] 的表单[。]|

    如果希望将表单的变体考虑在内（例如，单引号而不是双引号，或句号而不是问号），请为每个变体创建新模式。

4. 如果删除了 keyPhrase 实体，请将其添加回应用。 

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>测试自由格式的数据提取的新模式
1. 从顶栏中选择“测试”，打开测试面板。 

2. 输入以下话语： 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. 在结果下选择“检查”，查看实体和意向的测试结果。

    首先找到 `FormName` 实体，然后找到模式，并确定意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。

4. 选择顶部导航栏上的“测试”按钮，关闭测试面板。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程将示例话语添加到了现有的意向，然后针对表单名称创建了新 Pattern.any。 然后，本教程使用新的示例话语和实体创建了现有意向的模式。 交互式测试显示，由于找到了该实体，模式及其意向已被预测。 

> [!div class="nextstepaction"]
> [了解如何结合使用角色和模式](luis-tutorial-pattern-roles.md)