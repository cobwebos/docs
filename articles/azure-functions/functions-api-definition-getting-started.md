---
title: "Azure Functions 中 OpenAPI 元数据入门 | Microsoft Docs"
description: "Azure Functions 中 OpenAPI 支持入门"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: ad82c1a552d9d77259c44b938975eebc44933b86
ms.lasthandoff: 04/26/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>为 Function App 创建 OpenAPI 2.0 (Swagger) 元数据（预览版）

本文档分步演示如何为 Azure Functions 上托管的简单 API 创建 OpenAPI 定义。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>什么是 OpenAPI (Swagger)？
[Swagger 元数据](http://swagger.io/)是一个用于定义 API 功能和运行模式的文件，允许托管 REST API 的函数供各种其他软件使用。 诸如 PowerApps 和 [API 应用](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier)之类的 Microsoft 产品，以及如 [Postman](https://www.getpostman.com/docs/importing_swagger) 这样的第三方开发人员工具，还有[更多的软件包](http://swagger.io/tools/)都支持使用 Swagger 定义来使用 API。

## <a name="prepare-function"></a>使用简单的 API 创建函数
  若要创建 OpenAPI 定义，首先需要使用简单的 API 创建函数。 如果 Function App 上已存在托管的 API，则可以直接跳到下一部分
1. 创建一个新的 Function App。
  1. [Azure 门户](https://portal.azure.com) > `+ New` > 搜索“Function App”
1. 在新 Function App 内创建一个新的 HTTP 触发器函数
  1. 函数预先填充了定义非常简单的 REST API 的代码。
  1. 作为查询参数传递给函数或正文中的任何字符串都将返回为“Hello {input}”。
1. 转到新的 HTTP 触发器函数的 `Integrate` 选项卡
  1. 将 `Allowed HTTP methods` 切换到 `Selected methods`
  1. 在 `Selected HTTP methods` 中，取消选中 POST 以外的全部动词。
    ![所选的 HTTP 方法](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. 此步骤将在以后简化 API 定义。

## <a name="enable"></a>启用 API 定义支持
1. 导航到 `your function name` > `API Definition (preview)`
1. 将 `API Definition Source` 设置为 `Function`
  1. 此步骤为 Function App 启用一套 OpenAPI 选项，其中包括一个用于托管 Function App 域中的 OpenAPI 文件的终结点、一个 [OpenAPI 编辑器](http://editor.swagger.io)的内联副本和一个快速定义生成器。
![已启用的定义](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>通过模块创建 API 定义
1. 单击 `Generate API Definition template`
  1. 此步骤会在 Function App 中扫描 HTTP 触发器函数，并使用 functions.json 中的信息生成 OpenAPI 文档。
2. 将操作对象添加到 `paths: /api/yourfunctionroute post:`
  1. OpenAPI 快速入门文档是完整 OpenAPI 文档的大纲。 该文档需要更多元数据才能成为一个完整的 OpenAPI 定义，如操作对象和响应模板。
  1. 以下示例操作对象有一个已填充的生成/使用部分、一个参数对象和一个响应对象。
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary 在逻辑应用、流程和 PowerApps 中提供显示名称。
>
> 有关详细信息，请参阅[自定义 PowerApps 的 Swagger 定义](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)。

3. 单击 `save` 保存更改![添加模板定义](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>使用 API 定义
1. 复制 `API definition URL` 并将其粘贴到新的浏览器选项卡中，以查看原始的 OpenAPI 文档。
1. 可以将 OpenAPI 文档导入任意数量的工具，以便使用该 URL 进行测试和集成。
  1. 许多 Azure 资源能够使用保存在 Function 应用程序设置中的 API 定义 URL 自动导入 OpenAPI 文档。 作为 `Function` API 定义源的一部分，我们会为你更新该 URL。


## <a name="test-definition"></a>使用 Swagger UI 来测试 API 定义
嵌入式 API 定义编辑器的 UI 视图中内置了测试工具。 添加 API 密钥，然后使用每种方法下的 `Try this operation` 按钮。 工具将使用 API 定义来设置请求的格式，成功响应将表明定义正确。

### <a name="steps"></a>步骤：

1. 复制函数 API 密钥
  1. API 密钥可以在`function name` > `Keys` > `Function Keys`
  ![函数密钥](./media/functions-api-definition-getting-started/functionkey.png)下的 HTTP 触发器函数中找到
1. 导航到 `API Definition (preview)` 页。
  1. 单击 `Authenticate`，然后将函数 API 密钥添加到顶部的安全对象。
  ![OpenAPI 密钥](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. 选择 `/api/yourfunctionroute` > `POST`
1. 单击 `Try it out` 并输入要测试的名称
1. 在 `Pretty`
![API 定义测试](./media/functions-api-definition-getting-started/definitionTest.png)下应看到“成功”结果

## <a name="next-steps"></a>后续步骤
* [函数概述中的 OpenAPI 定义](functions-api-definition.md)
  * 有关 OpenAPI 支持的详细信息，请阅读完整文档。
* [Azure Functions 开发人员参考](functions-reference.md)  
  * ，用于编码函数和定义触发器及绑定的程序员参考。
* [Azure Functions Github 存储库](https://github.com/Azure/Azure-Functions/)
  * 查看 Functions Github，向我们提供有关 API 定义支持预览的反馈！ 为想要更新的任何内容制作一个 Github 发布。

