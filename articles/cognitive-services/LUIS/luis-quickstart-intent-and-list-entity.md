---
title: 教程：创建 LUIS 应用以获取与所列数据完全匹配的文本 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和列表实体创建一个简单的 LUIS 应用，以提取本快速入门中的数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 4ba2ba5d947a112f780579bf4b31ba38cb26ae03
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222964"
---
# <a name="tutorial-4-add-list-entity"></a>教程：4. 添加列表实体
在本教程中，我们将创建一个应用，用于演示如何获取与预定义列表匹配的数据。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解列表实体 
> * 创建意向为 MoveEmployee 且适用于人力资源 (HR) 领域的新 LUIS 应用
> * 添加列表实体以从陈述中提取员工
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

本文需要一个免费的 [LUIS](luis-reference-regions.md#luis-website) 帐户，以便能够创作 LUIS 应用程序。

## <a name="before-you-begin"></a>开始之前
如果尚未获得 [regex 实体](luis-quickstart-intents-regex-entity.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) Github 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `list`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="purpose-of-the-list-entity"></a>列表实体的用途
此应用预测的陈述涉及将员工从一个大楼移到另一个大楼。 此应用使用列表实体来提取员工。 可以使用姓名、电话号码、电子邮件或美国联邦社会安全号码来表示员工。 

列表实体可以存储许多项，每个项都有同义项。 对于中小型公司来说，列表实体用于提取员工信息。 

每个项的规范名称是员工编号。 就此域来说，同义项的示例如下： 

|同义项用途|同义项值|
|--|--|
|名称|John W. Smith|
|电子邮件地址|john.w.smith@mycompany.com|
|电话分机|x12345|
|个人移动电话号码|425-555-1212|
|美国联邦社会安全号码|123-45-6789|

出现以下情况时，列表实体非常适合此类数据：

* 数据值是已知的集。
* 此集不超出此实体类型的最大 LUIS [边界](luis-boundaries.md)。
* 陈述中的文本是同义项的完全匹配。 

LUIS 采用特定的方式来提取员工，因此可以由客户端应用程序创建一个进行员工移动的标准顺序。
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>添加 MoveEmployee 意向

1. LUIS 的“生成”部分包含你的人力资源应用。 在右上方的菜单栏中选择“生成”可切换到此部分。 

    [![LUIS 应用的屏幕截图，其中已突出显示右上方的导航栏](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-list-entity/hr-first-image.png#lightbox)

2. 选择“创建新意向”。 

    [![“意向”页的屏幕截图，其中已突出显示“创建新意向”按钮](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png)](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-button.png#lightbox)

3. 在弹出对话框中输入 `MoveEmployee`，然后选择“完成”。 

    ![“创建新意向”对话框的屏幕截图](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 将示例陈述添加到意向。

    |示例陈述|
    |--|
    |将 John W. Smith 从 B-1234 移到 H-4452|
    |将 john.w.smith@mycompany.com 从办公室 b-1234 移到办公室 h-4452|
    |明天将 x12345 换到 h-1234|
    |将 425-555-1212 置于 HH-2345|
    |将 123-45-6789 从 A-4321 移到 J-23456|
    |将 Jill Jones 从 D-2345 移到 J-23456|
    |将 jill-jones@mycompany.com 换到 M-12345|
    |将 x23456 移到 M-12345|
    |将 425-555-0000 移到 h-4452|
    |将 234-56-7891 移到 hh-2345|

    [![“意向”页的屏幕截图，其中已突出显示新陈述](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

## <a name="create-an-employee-list-entity"></a>创建员工列表实体
**MoveEmployee** 意向有陈述以后，LUIS 需了解什么是员工。 

1. 在左侧面板中选择“实体”。

    [ ![“意向”页的屏幕截图，在左侧导航中突出显示了“实体”按钮](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-select-entity-button.png#lightbox)

2. 选择“创建新实体”。

    [![“实体”页的屏幕截图，其中已突出显示“创建新实体”](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png) ](./media/luis-quickstart-intent-and-list-entity/hr-create-new-entity-button.png#lightbox)

3. 在实体弹出对话框中，输入 `Employee` 作为实体名称，输入“列表”作为实体类型。 选择“完成”。  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "创建新实体弹出对话框的屏幕截图")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. 在“员工”实体页上，输入 `Employee-24612` 作为新值。

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "输入值的屏幕截图")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. 对于“同义项”，请添加以下值：

    |同义项用途|同义项值|
    |--|--|
    |名称|John W. Smith|
    |电子邮件地址|john.w.smith@mycompany.com|
    |电话分机|x12345|
    |个人移动电话号码|425-555-1212|
    |美国联邦社会安全号码|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "输入同义项的屏幕截图")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. 输入 `Employee-45612` 作为新值。

7. 对于“同义项”，请添加以下值：

    |同义项用途|同义项值|
    |--|--|
    |名称|Jill Jones|
    |电子邮件地址|jill-jones@mycompany.com|
    |电话分机|x23456|
    |个人移动电话号码|425-555-0000|
    |美国联邦社会安全号码|234-56-7891|

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![训练应用](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "选择发布按钮的屏幕截图")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. 选择“生产”槽和“发布”按钮。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "选择“发布到生产槽”按钮的屏幕截图")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的话语查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "“发布”页上的终结点 URL 屏幕截图")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. 将光标定位到地址中 URL 的末尾，并输入 `shift 123-45-6789 from Z-1242 to T-54672`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回提取了 `Employee` 的 `MoveEmployee` 意向。

```JSON
{
  "query": "shift 123-45-6789 from Z-1242 to T-54672",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9882801
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    {
      "intent": "FindForm",
      "score": 0.016044287
    },
    {
      "intent": "GetJobInformation",
      "score": 0.007611245
    },
    {
      "intent": "ApplyForJob",
      "score": 0.007063288
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00684710965
    },
    {
      "intent": "None",
      "score": 0.00304174074
    },
    {
      "intent": "Utilities.Help",
      "score": 0.002981
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00212222221
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00191026414
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0007461446
    }
  ],
  "entities": [
    {
      "entity": "123 - 45 - 6789",
      "type": "Employee",
      "startIndex": 6,
      "endIndex": 16,
      "resolution": {
        "values": [
          "Employee-24612"
        ]
      }
    },
    {
      "entity": "123",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 8,
      "resolution": {
        "value": "123"
      }
    },
    {
      "entity": "45",
      "type": "builtin.number",
      "startIndex": 10,
      "endIndex": 11,
      "resolution": {
        "value": "45"
      }
    },
    {
      "entity": "6789",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 16,
      "resolution": {
        "value": "6789"
      }
    },
    {
      "entity": "-1242",
      "type": "builtin.number",
      "startIndex": 24,
      "endIndex": 28,
      "resolution": {
        "value": "-1242"
      }
    },
    {
      "entity": "-54672",
      "type": "builtin.number",
      "startIndex": 34,
      "endIndex": 39,
      "resolution": {
        "value": "-54672"
      }
    }
  ]
}
```

此员工已找到并已作为 `Employee` 类型返回，其解析值为 `Employee-24612`。

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>列表实体中的自然语言处理在何处发生？ 
由于列表实体是准确的文本匹配项，因此它不依赖于自然语言处理（或机器学习）。 LUIS 确实使用自然语言处理（或机器学习）来选择评分最高的正确意向。 此外，陈述中可能混合多个实体，甚至多个实体类型。 将为应用中的所有实体（包括自然语言处理（或机器学习）实体）处理每个陈述。

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用（包含列表实体）提取了正确的员工。 

现在，聊天机器人已获得足够的信息，可以确定主要操作 `MoveEmployee`，以及要移动的员工。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 在左上方的菜单上选择“Mu 应用”。 在应用列表中选择应用名称右侧的省略号 (***...***)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将分层实体添加到应用](luis-quickstart-intent-and-hier-entity.md)

