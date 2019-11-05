---
title: 教程：Pattern.any 实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 对于格式良好且数据结尾可能容易与话语的剩余单词混淆的话语，使用 pattern.any 实体可从中提取数据。
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 43ad6558b685d3959562be720f7494b3d2a7334e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499424"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>教程：使用 Pattern.any 实体提取自由格式数据

在本教程中，对于格式良好且数据结尾可能容易与话语的剩余单词混淆的话语，我们使用 pattern.any 实体从这些话语中提取数据。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**本教程介绍如何执行下列操作：**

> [!div class="checklist"]
> * 导入示例应用
> * 将示例话语添加到现有实体
> * 创建 Pattern.any 实体
> * 创建模式
> * 定型
> * 测试新模式

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>使用 Pattern.any 实体

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
|“公司新员工请求调职 2018 年第 5 版”  在哪里？|
|“公司新员工请求调职 2018 年第 5 版”  的作者是谁？|
|“公司新员工请求调职 2018 年第 5 版”  是以法语发布的吗？|

此可变长度包含可能导致 LUIS 无法正确识别实体结尾位置的单词。 在模式中使用 Pattern.any 实体，可指定表单名称的开头和结尾，以便 LUIS 正确提取表单名称。

|模板话语示例|
|--|
|{FormName} 在哪里[？]|
|{FormName} 的作者是谁[？]|
|{FormName} 是以法语发布的吗[？]|

## <a name="import-example-app"></a>导入示例应用

1. 下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json)。

1. 在 [LUIS 门户](https://www.luis.ai)的“我的应用”  页上，将 JSON 导入到新应用中。

1. 在“管理”  部分的“版本”  选项卡上，克隆版本并将其命名为 `patt-any`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="add-example-utterances"></a>添加示例话语 

1. 从顶部导航栏中选择“生成”  ，然后从左侧导航栏中选择“意向”  。

1. 从“意向”列表中选择“FindForm”  。

1. 添加一些示例话语：

    |示例陈述|
    |--|
    |“实验室着火时该怎么办”  的表单在哪里，在我阅读该表单后需要谁签名？|
    |“公司新员工请求调职”  在服务器上的哪里？|
    |“主要校园的健康和保健请求”  的作者是谁并且最新版本是什么？|
    |我正在查找名为“包括物理资产的 Office 移动请求”  的表单。 |

    如果没有 Pattern.any 实体，则 LUIS 很难理解表单标题的结束位置，因为表单名称有很多种变体。

## <a name="create-a-patternany-entity"></a>创建 Pattern.any 实体
Pattern.any 实体提取不同长度的实体。 它仅适用于模式，因为模式会标记实体的开头和结尾。  

1. 在左侧导航栏中选择“意向”  。

1. 选择“创建新实体”  ，输入名称 `FormName`，然后选择“Pattern.any”  作为类型。 选择“完成”  。 

    由于 Pattern.any 仅在模式中才有效，因此，不能在意向的示例话语中标记实体。 

    如果想要使提取的数据包含 number 或 datetimeV2 等其他实体，则需要创建包含 Pattern.any、number 和 datetimeV2 的复合实体。

## <a name="add-a-pattern-that-uses-the-patternany"></a>添加使用 Pattern.any 的模式

1. 从左侧导航栏中选择“模式”  。

1. 选择“FindForm”意向  。

1. 输入以下使用新实体的模板话语：

    |模板话语|
    |--|
    |["]{FormName}["] 表单在哪里，在我阅读该表单后需要谁签名[？]|
    |["]{FormName}["] 在服务器上的哪里[？]|
    |["]{FormName}["] 的作者是谁并且最新版本是什么[？]|
    |我正在查找名为 ["]{FormName}["] 的表单[。]|

    如果希望将表单的变体考虑在内（例如，单引号而不是双引号，或句号而不是问号），请为每个变体创建新模式。

## <a name="train-the-luis-app"></a>训练 LUIS 应用

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>测试自由格式的数据提取的新模式
1. 从顶栏中选择“测试”，打开测试面板  。 

1. 输入以下话语： 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. 在结果下选择“检查”，查看实体和意向的测试结果  。

    首先找到 `FormName` 实体，然后找到模式，并确定意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。

1. 选择顶部导航栏上的“测试”按钮，关闭测试面板  。

## <a name="using-an-explicit-list"></a>使用显式列表

如果发现模式在包含 Pattern.any 时错误提取实体，请使用[显式列表](luis-concept-patterns.md#explicit-lists)来更正此问题。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程将示例话语添加到了现有的意向，然后针对表单名称创建了新 Pattern.any。 然后，本教程使用新的示例话语和实体创建了现有意向的模式。 交互式测试显示，由于找到了该实体，模式及其意向已被预测。 

> [!div class="nextstepaction"]
> [了解如何结合使用角色和模式](luis-tutorial-pattern-roles.md)
