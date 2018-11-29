---
title: 教程 4：提取文本匹配项 - LUIS 列表实体
titleSuffix: Azure Cognitive Services
description: 获取与项的预定义列表匹配的数据。 列表中的每个项可以有也是完全匹配的同义词
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a4e294687b6c3ea2ba6ff8003e7a8f1ac69ea639
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425065"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>教程 4：提取确切的文本匹配项
本教程介绍如何获取与项的预定义列表匹配的数据。 列表中的每个项可以包含一个同义词列表。 就人力资源应用来说，可以通过多项信息（例如姓名、电子邮件、电话号码、美国联邦税务 ID）来确定员工身份。 

人力资源应用需确定哪位员工在从一个建筑移到另一个建筑。 对于有关员工移动情况的话语，LUIS 会先确定意向，然后提取员工，以便通过客户端应用程序创建一个进行员工移动的标准顺序。

此应用使用列表实体来提取员工。 可以使用姓名、公司电话分机、移动电话号码、电子邮件或美国联邦社会安全号码来表示员工。 

出现以下情况时，列表实体非常适合此类数据：

* 数据值是已知的集。
* 此集不超出此实体类型的最大 LUIS [边界](luis-boundaries.md)。
* 话语中的文本是同义项或规范名称的完全匹配。 

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用现有的教程应用
> * 添加 MoveEmployee 意向
> * 添加列表实体 
> * 定型 
> * 发布
> * 从终结点获取意向和实体

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用
继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `list`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。 


## <a name="moveemployee-intent"></a>MoveEmployee 意向

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 选择“创建新意向”。 

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

    请记住，数字和 datetimeV2 是在前面的某个教程中添加的，因此在示例话语中发现它们时，系统会自动对其进行标记。

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>员工列表实体
**MoveEmployee** 意向有示例话语以后，LUIS 需了解什么是员工。 

每个项的主规范名称是员工编号。 就此域来说，每个规范名称的同义项的示例如下： 

|同义项用途|同义项值|
|--|--|
|名称|John W. Smith|
|电子邮件地址|john.w.smith@mycompany.com|
|电话分机|x12345|
|个人移动电话号码|425-555-1212|
|美国联邦社会安全号码|123-45-6789|


1. 在左侧面板中选择“实体”。

2. 选择“创建新实体”。

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

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>从终结点获取意向和实体

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

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

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
本教程创建了新意向，添加了示例话语，然后创建了列表实体以便从话语中提取确切的文本匹配项。 在训练并发布应用后，向终结点发出的查询识别了意向，并返回了提取的数据。

> [!div class="nextstepaction"]
> [将分层实体添加到应用](luis-quickstart-intent-and-hier-entity.md)

