---
title: 使用 pattern.any 实体改进 LUIS 预测的教程 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 本教程介绍如何使用 pattern.any 实体改进 LUIS 意向和实体预测。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: ca4ed4444cd753c0fe22c6596dfd34d033b7b0cf
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236996"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>教程：使用 pattern.any 实体改进应用

本教程介绍如何使用 pattern.any 实体改进意向和实体预测。  

> [!div class="checklist"]
* 了解何时以及如何使用 pattern.any
* 创建使用 pattern.any 的模式
* 如何验证预测改进

本文需要一个免费的 [LUIS](luis-reference-regions.md) 帐户，以便创作 LUIS 应用程序。

## <a name="before-you-begin"></a>开始之前
如果还没有[模式角色](luis-tutorial-pattern-roles.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) GitHub 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `patt-any`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="the-purpose-of-patternany"></a>Pattern.any 的用途
通过 pattern.any 实体，可找到任意形式的数据，其中实体的用语方式比较晦涩，难以从话语的其余部分来确定实体的结尾。 

此人力资源应用有助于员工查找公司表单。 [正则表达式教程](luis-quickstart-intents-regex-entity.md)中已添加这些表单。 该教程中的表单名称使用正则表达式来提取格式正确的表单名称，如以下话语表中为粗体的表单名称：

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

此可变长度包含可能导致 LUIS 无法正确识别实体结尾位置的短语。 在模式中使用 Pattern.any 实体，可指定表单名称的开头和结尾，以便 LUIS 正确提取表单名称。

**尽管模式允许提供较少的示例话语，但如果未检测到实体，则该模式将不匹配。**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>将示例话语添加到现有意向 FindForm 
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

5. 将应用定型。


## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>测试自由格式的数据提取的新模式
1. 从顶栏中选择“测试”，打开测试面板。 

2. 输入以下话语： 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. 在结果下选择“检查”，查看实体和意向的测试结果。

    首先找到 `FormName` 实体，然后找到模式，并确定意向。 如果测试结果中未检测到实体，因而未找到模式，则需要在意向（而不是模式）上添加更多示例陈述。

4. 选择顶部导航栏上的“测试”按钮，关闭测试面板。

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，在应用列表中选择应用名称右侧的省略号 (***...***)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何结合使用角色和模式](luis-tutorial-pattern-roles.md)