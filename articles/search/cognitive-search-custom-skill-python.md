---
title: 自定义技能示例 (Python)
titleSuffix: Azure Cognitive Search
description: 本文向 Python 开发人员介绍使用 Azure Functions 和 Visual Studio 生成自定义技能的工具和技术。 自定义技能包含用户定义的模型或逻辑，可将这些模型或逻辑添加到 Azure 认知搜索中的 AI 扩充索引管道。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936123"
---
# <a name="example-create-a-custom-skill-using-python"></a>示例：使用 Python 创建自定义技能

此 Azure 认知搜索技能集示例演示如何使用 Python 和 Visual Studio Code 创建 Web API 自定义技能。 该示例使用一个实现[自定义技能接口](cognitive-search-custom-skill-interface.md)的 [Azure 函数](https://azure.microsoft.com/services/functions/)。

该自定义技能在设计上非常简单（串联两个字符串），使你可以专注于在 Python 中进行自定义技能开发所用的工具和技术。 成功创建一个简单的技能后，便可以在更复杂的方案中运用这些知识。

## <a name="prerequisites"></a>先决条件

+ 查看[自定义技能接口](cognitive-search-custom-skill-interface.md)，其中介绍了自定义技能应该实现的输入/输出接口。

+ 设置环境 我们已根据[此端到端教程](/azure/python/tutorial-vs-code-serverless-python-01)使用 Visual Studio Code 和 Python 扩展设置了无服务器 Azure 函数。 本教程将引导你安装以下工具和组件： 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [适用于 Visual Studio Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>创建 Azure 函数

此示例使用 Azure 函数来演示托管 Web API 的概念，但也可以使用其他方法。 只要满足[认知技能的接口需求](cognitive-search-custom-skill-interface.md)，采用的方法并不重要。 但是，可通过 Azure Functions 轻松创建自定义技能。

### <a name="create-a-function-app"></a>创建函数应用

使用 Visual Studio Code 中的 Azure Functions 项目模板可创建一个项目，该项目可发布到 Azure 中的函数应用。 函数应用可将函数分组为一个逻辑单元，以用于管理、部署和共享资源。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择 `Azure Functions: Create new project...`。

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 出于此原因，请不要选择某个工作区中的项目文件夹。

1. 选择函数应用项目的语言。 对于本教程，请选择“Python”。 
1. 选择 Python 版本（Azure Functions 支持版本 3.7.5）
1. 为项目的第一个函数选择模板。 选择“HTTP 触发器”，在新的函数应用中创建一个 HTTP 触发的函数。 
1. 提供函数名称。 在本例中，我们将使用名称 **Concatenator** 
1. 选择“函数”作为授权级别。  这意味着，我们将提供一个[函数密钥](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)来调用函数的 HTTP 终结点。 
1. 选择打开项目的方式。 对于此步骤，请选择“添加到工作区”，在当前工作区中创建函数应用。 

Visual Studio Code 将在新的工作区中创建函数应用项目。 此项目包含 [host.json](../azure-functions/functions-host-json.md) 和 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 配置文件以及任何特定于语言的项目文件。 

同时会在函数应用项目的 **Concatenator** 文件夹中创建一个新的 HTTP 触发函数。 此文件夹中有一个名为“\_\_init__.py”的包含以下内容的文件：

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

现在，让我们修改该代码来跟踪[自定义技能接口](cognitive-search-custom-skill-interface.md)。 使用以下内容修改代码：

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

**transform_value** 方法对单个记录执行操作。 可以根据具体的需求修改该方法。 请记得执行任何必要的输入验证，并在无法对该记录完成操作时返回生成的任何错误和警告。

### <a name="debug-your-code-locally"></a>在本地调试代码

在 Visual Studio Code 中可以轻松调试代码。 按 F5，或转到“调试”菜单并选择“开始调试”。  

在关注的行中按 F9 可以在代码中设置任何断点。

开始调试后，函数将在本地运行。 可以使用 Postman 或 Fiddler 等工具向 localhost 发出请求。 请注意终端窗口中本地终结点的位置。 

## <a name="publish-your-function"></a>发布函数

当你对函数行为满意时，可将其发布。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择“部署到函数应用...”。  

1. 选择要在其中部署应用程序的 Azure 订阅。

1. 选择“+ 在 Azure 中创建新的函数应用” 

1. 输入函数应用的全局唯一名称。

1. 选择 Python 版本（Python 3.7.x 适用于此函数）。

1. 选择新资源的位置 (例如 "美国西部 2") 。

此时，将在 Azure 订阅中创建所需的资源用于在 Azure 上托管新的 Azure 函数。 等待部署完成。 输出窗口将显示部署过程的状态。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到“所有资源”并按名称查找发布的函数。  如果已将该资源命名为 **Concatenator**，请选择它。

1. 单击“</> 获取函数 URL”按钮。  可以复制该 URL 来调用函数。

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

此示例生成的结果应与之前在本地环境中运行函数时看到的结果相同。

## <a name="connect-to-your-pipeline"></a>连接到管道

现在有了新的自定义技能，可将其添加到技能组合。 以下示例演示如何调用技能，以将文档的标题和作者串联到名为 merged_title_author 的单个字段中。 请将 `[your-function-url-here]` 替换为新 Azure 函数的 URL。

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
祝贺！ 现已创建第一个自定义技能。 现在，可按照相同的模式添加自己的自定义功能。 单击以下链接了解详细信息。

+ [强大技能：自定义技能的存储库](https://github.com/Azure-Samples/azure-search-power-skills)
+ [将自定义技能添加到 AI 扩充管道](cognitive-search-custom-skill-interface.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)