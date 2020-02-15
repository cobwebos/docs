---
title: 自定义技能示例（Python）
titleSuffix: Azure Cognitive Search
description: 对于 Python 开发人员，请参阅使用 Azure Functions 和 Visual Studio 生成自定义技能的工具和技术。 自定义技能包含用户定义的模型或逻辑，你可以将其添加到 Azure 认知搜索中的 AI 扩充索引管道。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210459"
---
# <a name="example-create-a-custom-skill-using-python"></a>示例：使用 Python 创建自定义技能

在此 Azure 认知搜索技能组合示例中，你将了解如何使用 Python 和 Visual Studio Code 创建 web API 自定义技能。 该示例使用实现[自定义技能接口](cognitive-search-custom-skill-interface.md)的[Azure 函数](https://azure.microsoft.com/services/functions/)。

自定义技能在设计上是简单的（它连接两个字符串），以便您可以专注于 Python 中用于自定义技能开发的工具和技术。 在您成功完成一种简单的技能后，您可以使用更复杂的方案进行分支。

## <a name="prerequisites"></a>必备条件

+ 查看[自定义技能界面](cognitive-search-custom-skill-interface.md)，了解自定义技能应该实现的输入/输出接口简介。

+ 设置您的环境。 [本教程以端到端的结尾](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01)，使用 Visual Studio Code 和 Python 扩展设置无服务器的 Azure 函数。 本教程将引导你完成以下工具和组件的安装： 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [适用于 Visual Studio Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>创建 Azure 函数

此示例使用 Azure 函数来演示托管 web API 的概念，但也可以使用其他方法。 只要满足[认知技能的接口需求](cognitive-search-custom-skill-interface.md)，采用的方法并不重要。 但是，可通过 Azure Functions 轻松创建自定义技能。

### <a name="create-a-function-app"></a>创建函数应用

使用 Visual Studio Code 中的 Azure Functions 项目模板可创建一个项目，该项目可发布到 Azure 中的函数应用。 函数应用可将函数分组为一个逻辑单元，以用于管理、部署和共享资源。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择 `Azure Functions: Create new project...`。

1. 为项目工作区选择目录位置，然后选择“选择”。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 出于此原因，请不要选择作为工作区一部分的项目文件夹。

1. 选择函数应用项目的语言。 对于本教程，请选择 " **Python**"。
1. 选择 Python 版本（Azure Functions 支持版本3.7.5）
1. 为项目的第一个函数选择模板。 选择 " **http 触发器**"，在新的 function app 中创建 http 触发的函数。
1. 提供函数名称。 在这种情况下，我们将使用**连接符** 
1. 选择 "**函数**" 作为授权级别。 这意味着我们将提供[函数密钥](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)来调用函数的 HTTP 终结点。 
1. 选择要打开项目的方式。 对于此步骤，请选择 "**添加到工作区**" 以在当前工作区中创建函数应用。

Visual Studio Code 将在新的工作区中创建函数应用项目。 此项目包含 [host.json](../azure-functions/functions-host-json.md) 和 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 配置文件以及任何特定于语言的项目文件。 

还会在函数应用项目的**连接符**文件夹中创建一个新的 HTTP 触发函数。 在此示例中，会出现一个名为 "\_\_init__" 的文件，其中包含以下内容：

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

现在，让我们修改该代码以遵循[自定义技能界面](cognitive-search-custom-skill-interface.md)。 修改包含以下内容的代码：

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

**Transform_value**方法对单个记录执行操作。 您可以修改该方法以满足您的特定需要。 请记住，执行任何必要的输入验证，如果无法完成记录的操作，则返回生成的任何错误和警告。

### <a name="debug-your-code-locally"></a>在本地调试代码

Visual Studio Code 可以轻松地调试代码。 按 "F5" 或 "**调试**" 菜单，然后选择 "**启动调试**"。

您可以通过在相关行上按 "F9" 来设置代码的任何断点。

开始调试后，函数将在本地运行。 可以使用 Postman 或 Fiddler 等工具向 localhost 发出请求。 请注意终端窗口上本地终结点的位置。 

## <a name="publish-your-function"></a>发布函数

对函数行为感到满意后，可以将其发布。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择 "**部署到 Function App ...** "。 

1. 选择要在其中部署应用程序的 Azure 订阅。

1. **在 Azure 中选择 "+ 创建新 Function App"**

1. 输入 function app 的全局唯一名称。

1. 选择 Python 版本（Python 3.7. x 适用于此函数）。

1. 选择新资源的位置（例如 "美国西部 2"）。

此时，将在 Azure 订阅中创建所需的资源，以便在 Azure 上托管新的 Azure 函数。 等待部署完成。 "输出" 窗口将显示部署过程的状态。

1. 在[Azure 门户](https://portal.azure.com)中，导航到 "**所有资源**"，并查找按名称发布的函数。 如果已将其命名为**连接符**，请选择资源。

1. 单击 " **</> 获取函数 URL** " 按钮。 这将允许您复制 URL 以调用函数。

## <a name="test-the-function-in-azure"></a>在 Azure 中测试函数

现在有了默认主机密钥，按以下方式测试函数：

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>请求正文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

在本地环境中运行函数时，此示例应生成与之前看到的结果相同的结果。

## <a name="connect-to-your-pipeline"></a>连接到管道

现在有了新的自定义技能，可将其添加到技能组合。 下面的示例演示如何调用技能，将文档的标题和作者连接到一个字段，该字段将 merged_title_author。 将 `[your-function-url-here]` 替换为新的 Azure 函数的 URL。

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>后续步骤
祝贺你！ 您已经创建了您的第一个自定义技巧。 现在，可按照相同的模式添加自己的自定义功能。 单击下面的链接可了解详细信息。

+ [强大技能：定制技能的存储库](https://github.com/Azure-Samples/azure-search-power-skills)
+ [向 AI 扩充管道添加自定义技能](cognitive-search-custom-skill-interface.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)
