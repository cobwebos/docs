---
title: 自定义技能示例（Python）
titleSuffix: Azure Cognitive Search
description: 对于 Python 开发人员，了解使用 Azure 函数和可视化工作室构建自定义技能的工具和技术。 自定义技能包含用户定义的模型或逻辑，您可以将这些模型或逻辑添加到 Azure 认知搜索中丰富 AI 的索引管道中。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210459"
---
# <a name="example-create-a-custom-skill-using-python"></a>示例：使用 Python 创建自定义技能

在此 Azure 认知搜索技能集示例中，您将学习如何使用 Python 和 Visual Studio 代码创建 Web API 自定义技能。 该示例使用实现[自定义技能接口](cognitive-search-custom-skill-interface.md)的[Azure 函数](https://azure.microsoft.com/services/functions/)。

自定义技能在设计上很简单（它串联了两个字符串），因此您可以专注于用于 Python 中自定义技能开发的工具和技术。 使用简单技能成功后，可以使用更复杂的方案进行分支。

## <a name="prerequisites"></a>先决条件

+ 查看[自定义技能接口](cognitive-search-custom-skill-interface.md)，深入了解自定义技能应实现的输入/输出接口。

+ 设置环境。 我们遵循[本教程端到端地](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01)使用 Visual Studio 代码和 Python 扩展设置无服务器 Azure 函数。 本教程将引导您完成以下工具和组件的安装： 

  + [Python 3.75](https://www.python.org/downloads/release/python-375/)
  + [视觉工作室代码](https://code.visualstudio.com/)
  + [可视化工作室代码的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure 函数核心工具](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [适用于 Visual Studio Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>创建 Azure 函数

此示例使用 Azure 函数演示托管 Web API 的概念，但可以采用其他方法。 只要满足[认知技能的接口需求](cognitive-search-custom-skill-interface.md)，采用的方法并不重要。 但是，可通过 Azure Functions 轻松创建自定义技能。

### <a name="create-a-function-app"></a>创建函数应用

使用 Visual Studio Code 中的 Azure Functions 项目模板可创建一个项目，该项目可发布到 Azure 中的函数应用。 函数应用可将函数分组为一个逻辑单元，以用于管理、部署和共享资源。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择 `Azure Functions: Create new project...`。

1. 为项目工作区选择目录位置，然后选择“选择”****。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 因此，不要选择属于工作区的项目文件夹。

1. 为函数应用项目选择语言。 对于本教程，请选择**Python**。
1. 选择 Python 版本（Azure 函数支持版本 3.7.5）
1. 为项目的第一个函数选择模板。 选择**HTTP 触发器**以在新函数应用中创建 HTTP 触发功能。
1. 提供函数名称。 在这种情况下，让我们使用**串联** 
1. 选择 **"功能**"作为授权级别。 这意味着我们将提供[一个函数键](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)来调用函数的 HTTP 终结点。 
1. 选择要如何打开项目。 对于此步骤，选择 **"添加到工作区"** 以在当前工作区中创建函数应用。

Visual Studio Code 将在新的工作区中创建函数应用项目。 此项目包含 [host.json](../azure-functions/functions-host-json.md) 和 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 配置文件以及任何特定于语言的项目文件。 

在函数应用项目的 **"Concatenator"** 文件夹中也创建了新的 HTTP 触发函数。 里面将有一个名为"init__.py"\_\_的文件，内容包括：

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

**transform_value**方法对单个记录执行操作。 您可以修改方法以满足您的特定需求。 请记住执行任何必要的输入验证，并返回如果无法完成该记录的操作时生成的任何错误和警告。

### <a name="debug-your-code-locally"></a>在本地调试代码

可视化工作室代码使调试代码变得容易。 按"F5"或转到**调试**菜单并选择 **"开始调试**"。

您可以通过在感兴趣行上点击"F9"来设置代码上的任何断点。

开始调试后，函数将在本地运行。 您可以使用邮递员或 Fiddler 等工具向本地主机发出请求。 请注意本地终结点在终端窗口中的位置。 

## <a name="publish-your-function"></a>发布函数

当您对函数行为感到满意时，可以发布它。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令调色板中，搜索并选择 **"部署到函数应用..."** 

1. 选择要在其中部署应用程序的 Azure 订阅。

1. 选择 **= 在 Azure 中创建新功能应用**

1. 为函数应用输入全局唯一名称。

1. 选择 Python 版本（Python 3.7.x 适用于此功能）。

1. 为新资源选择位置（例如，美国西部 2）。

此时，将在 Azure 订阅中创建必要的资源，以在 Azure 上托管新的 Azure 函数。 等待部署完成。 输出窗口将显示部署过程的状态。

1. 在[Azure 门户](https://portal.azure.com)中，导航到 **"所有资源"，** 并查找按其名称发布的函数。 如果将其命名为 **"串联"，** 请选择资源。

1. 单击 **"</>获取功能 URL"** 按钮。 这将允许您复制 URL 来调用函数。

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

此示例应产生与以前在本地环境中运行函数时看到的结果相同的结果。

## <a name="connect-to-your-pipeline"></a>连接到管道

现在有了新的自定义技能，可将其添加到技能组合。 下面的示例演示如何调用技能将文档的标题和作者串联到我们称之为merged_title_author的单个字段中。 替换为`[your-function-url-here]`新的 Azure 函数的 URL。

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
祝贺你！ 您已经创建了第一个自定义技能。 现在，可按照相同的模式添加自己的自定义功能。 单击以下链接以了解更多信息。

+ [权力技能：自定义技能的宝库](https://github.com/Azure-Samples/azure-search-power-skills)
+ [向 AI 扩充管道添加自定义技能](cognitive-search-custom-skill-interface.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)
