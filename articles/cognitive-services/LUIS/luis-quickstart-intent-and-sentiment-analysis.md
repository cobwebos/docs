---
title: 情绪分析
titleSuffix: Azure Cognitive Services
description: 在本教程中，我们将创建一个应用，用于演示如何从陈述中提取积极、消极和中性的情绪。 情绪是从整个话语确定的。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d93c7619bb670a81372ab83359836a78b8956b09
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098911"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>教程 9：提取整体话语的情绪
在本教程中，我们将创建一个应用，用于演示如何从陈述中提取积极、消极和中性的情绪。 情绪是从整个话语确定的。

情绪分析是确定用户陈述是积极、消极还是中性的功能。 

以下陈述显示了情绪的示例：

|情绪|得分|陈述|
|:--|:--|:--|
|积极|0.91 |John W. Smith 在巴黎的演讲做得很好。|
|积极|0.84 |jill-jones@mycompany.com 对 Parker 的推销工作做得很好。|

情绪分析是一种适用于所有话语的发布设置。 你不必在陈述中找到表示情感的词语并为它们加注标签，因为情绪分析应用于整个陈述。 

因为它是发布设置，所以不会在意向或实体页上看到它。 可以在[交互式测试](luis-interactive-test.md#view-sentiment-results)窗格中或在终结点 URL 处进行测试时看到它。 

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用现有的教程应用 
> * 将情绪分析添加为发布设置
> * 定型
> * 发布
> * 从终结点获取获取话语的情绪

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>使用现有应用

继续使用上一个教程中创建的名为 **HumanResources** 的应用。 

如果没有上一个教程中的 HumanResources 应用，请执行以下步骤：

1.  下载并保存[应用 JSON 文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json)。

2. 将 JSON 导入到新应用中。

3. 在“管理”部分的“版本”选项卡上，克隆版本并将其命名为 `sentiment`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 由于版本名称用作 URL 路由的一部分，因此该名称不能包含任何在 URL 中无效的字符。

## <a name="employeefeedback-intent"></a>EmployeeFeedback 意向 
添加一个新意向来从公司的成员捕获员工反馈。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 选择“创建新意向”。

3. 将新意向命名为 `EmployeeFeedback`。

    ![以 EmployeeFeedback 为名称创建新意向的对话框](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 添加几项陈述来指明员工某项工作做得很好或者某个方面需要改进：

    请记住，在此人力资源应用中，员工是在列表实体 `Employee` 中通过姓名、电子邮件、电话分机号码、移动电话号码及其美国联邦社会安全号码定义的。 

    |陈述|
    |--|
    |425-555-1212 做了很好的工作来欢迎一位同事从产假归来。|
    |234-56-7891 在一位同事悲伤时做了很好的安慰工作。|
    |jill-jones@mycompany.com 没有文书工作所需的所有发票。|
    |john.w.smith@mycompany.com 在一个月后上交了没有签名的必需表单|
    |x23456 没有赶上重要的市场营销场外会议。|
    |x12345 错过了六月份的评审会议。|
    |Jill Jones 在哈佛进行了令人震撼的推销|
    |John W. Smith 在斯坦福的演讲做得很好|

    [ ![在 EmployeeFeedback 意向中包含了示例陈述的 LUIS 应用的屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>定型

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>将应用配置为包含情绪分析
1. 在右上角的导航栏中选择“管理”，然后从左侧菜单中选择“发布设置”。

2. 切换到“情绪分析”以启用此设置。 

    ![开启“情绪分析”作为发布设置](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>发布

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>从终结点获取获取话语的情绪

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 将光标定位到地址中 URL 的末尾，并输入 `Jill Jones work with the media team on the public portal was amazing`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含所提取的情绪分析的 `EmployeeFeedback` 意向。
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    sentimentAnalysis 是积极的，得分为 0.86。 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤
本教程将情绪分析添加为发布设置，以从整个话语中提取情绪值。

> [!div class="nextstepaction"] 
> [在 HR 应用中审核终结点表述](luis-tutorial-review-endpoint-utterances.md) 

