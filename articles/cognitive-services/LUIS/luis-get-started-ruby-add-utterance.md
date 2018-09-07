---
title: 快速入门 - 使用 Ruby 更改模型并训练 LUIS 应用 - Azure 认知服务 | Microsoft Docs
description: 此 Ruby 快速入门将示例话语添加到家庭自动化应用并训练该应用。 示例话语是映射到意向的对话式用户文本。 通过提供意向的示例话语，可以教 LUIS 用户提供的文本类型属于哪种意向。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 4df6352c140328b93044fd2340fb7c46d44e1b04
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43768804"
---
# <a name="quickstart-change-model-using-ruby"></a>快速入门：使用 Ruby 更改模型

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Ruby](http://rubyinstaller.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>创建快速入门代码 

将依赖项添加到名为 `add-utterances.rb` 的文件中。

   [!code-ruby[Ruby and LUIS Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=1-21 "Ruby and LUIS Dependencies")]

添加用于训练状态的 GET 请求。

   [!code-ruby[SendGet](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=23-33 "SendGet")]

添加用于创建陈述或开始训练的 POST 请求。 

   [!code-ruby[SendPost](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=35-47 "SendPost")]

添加 `AddUtterances` 函数。

   [!code-ruby[AddUtterances method](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=49-54 "AddUtterances method")]


添加 `Train` 函数。 

   [!code-ruby[Train](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=56-62 "Train")]

添加 `Status` 函数。

   [!code-ruby[Status](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=64-68 "Status")]

若要管理参数，请添加 main 代码。

   [!code-ruby[Main code](~/samples-luis/documentation-samples/quickstarts/change-model/ruby/add-utterances.rb?range=70-72 "Main code")]

## <a name="run-code"></a>运行代码

使用 Ruby 从命令行运行该应用程序。

### <a name="add-an-utterance-from-the-command-line"></a>从命令行添加陈述

调用 `add-utterances.rb` 将添加话语、训练并获得训练状态。

```CMD
> ruby add-utterances.rb 
```

此结果显示调用“添加陈述”API 的结果。 添加的陈述的 `response` 字段采用此格式。 `hasError` 为 false，表示已添加陈述。  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

下一个响应显示排队的训练。 然后下一个响应显示每个意向的状态。 

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>清理资源
完成此快速入门后，删除此快速入门中创建的所有文件。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [以编程方式生成 LUIS 应用](luis-tutorial-node-import-utterances-csv.md)