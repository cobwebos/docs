---
title: 使用 Azure Functions 在 Azure 逻辑应用中添加并运行自定义代码 | Microsoft Docs
description: 了解如何使用 Azure Functions 在 Azure 逻辑应用中添加并运行自定义代码片段
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263184"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>使用 Azure Functions 在 Azure 逻辑应用中添加并运行自定义代码片段

若想在逻辑应用中创建并运行足够处理特定问题的代码，可以使用 [Azure Functions](../azure-functions/functions-overview.md) 创建自己的函数。 使用此服务提供的功能，可以在逻辑应用中创建并运行以 Node.js 或 C# 编写的自定义代码片段，且无需为运行代码创建整个应用或基础结构。 Azure Functions 在云中提供无服务器计算，且对执行任务非常有用，如以下示例：

* 通过受 Node.js 或 C# 支持的函数扩展逻辑应用的行为。
* 在逻辑应用工作流中执行计算。
* 在逻辑应用中应用高级格式设置或计算字段。

还能[从 Azure Functions 内部调用逻辑应用](#call-logic-app)。

## <a name="prerequisites"></a>先决条件

若要遵循本文，需要准备好以下各项：

* 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 要在其中添加函数的逻辑应用

  如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 用作逻辑应用中第一个步骤的[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  在添加运行函数的操作之前，必须使用触发器启动逻辑应用。

* 一个 Azure 函数应用，它是 Azure Functions 和你的 Azure 函数的容器。 如果没有函数应用，则必须[先创建函数应用](../azure-functions/functions-create-first-azure-function.md)。 然后才可以在逻辑应用设计器中[在逻辑应用外部单独](#create-function-external)创建函数或[从逻辑应用内部](#create-function-designer)创建函数。

  无论 Azure 函数应用和函数是新建的还是现有的，在使用逻辑应用方面都具备相同的要求：

  * 函数应用必须与逻辑应用属于同一 Azure 订阅。

  * 函数必须使用 JavaScript 或 C# 的泛型 Webhook 函数模板。 此模板能接受逻辑应用中 `application/json` 类型的内容。 在将这些函数添加到逻辑应用时，这些模板还能帮助逻辑应用设计器查找并显示使用这些模板创建的自定义函数。

  * 检查函数模板的“模式”属性是否设为“Webhook”，以及“Webhook 类型”属性是否设为“泛型 JSON”。

    1. 登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。
    2. 在 Azure 主菜单中，选择“函数应用”。 
    3. 在“函数应用”列表中选择自己的函数应用，展开函数并选择“集成”。 
    4. 检查模板的“模式”属性是否设为“Webhook”，以及“Webhook 类型”属性是否设为“泛型 JSON”。 

  * 若函数具备 [API 定义](../azure-functions/functions-openapi-definition.md)（以前称为 [Swagger 文件](http://swagger.io/)），则逻辑应用设计器会在为使用函数参数提供更丰富的体验。 
  在逻辑应用查找并访问具备 Swagger 描述的函数之前，请先[按以下步骤设置函数应用](#function-swagger)。

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>在逻辑应用外部创建函数

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中创建 Azure 函数应用，该应用必须具备与逻辑应用一样的 Azure 订阅，然后创建 Azure 函数。 若是初次接触 Azure Functions，则需要了解如何[在 Azure 门户中创建第一个函数](../azure-functions/functions-create-first-azure-function.md)，不过请注意有关创建可从逻辑应用添加并调用的 Azure 函数的要求。

* 请确保选择 JavaScript 或 C# 的泛型 Webhook 函数模板。

  ![泛型 Webhook - JavaScript 或 C#](./media/logic-apps-azure-functions/generic-webhook.png)

* 创建 Azure 函数后，检查模板的“模式”和“Webhook 类型”属性是否正确设置。

  1. 在“函数应用”列表中展开函数并选择“集成”。 

  2. 检查模板的“模式”属性是否设为“Webhook”，以及“Webhook 类型”属性是否设为“泛型 JSON”。 

     ![函数模板的“集成”属性](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* （可选）若为函数[生成了 API 定义](../azure-functions/functions-openapi-definition.md)（以前称为 [Swagger 文件](http://swagger.io/)），则在逻辑应用设计器中使用函数参数时能得到更丰富的体验。 若要设置函数应用以便让逻辑应用查找并访问具备 Swagger 描述的函数，请执行以下操作：

  * 确保函数应用正在运行。

  * 在函数应用中，设置[跨源资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 以便允许所有来源：

    1. 从“函数应用”列表开始，选择函数应用，然后选择“平台功能”>“CORS”。

       ![选择函数应用 >“平台功能”>“CORS”](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. 在“CORS”下，添加  **通配符，但删除列表中的所有其他来源并选择“保存”**`*`。

       ![选择函数应用 >“平台功能”>“CORS”](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>访问 HTTP 请求中的属性值

Webhook 函数能接受 HTTP 请求作为输入，并将这些请求传递至其他函数。 例如，尽管逻辑应用具有[转换 DateTime 值的函数](../logic-apps/workflow-definition-language-functions-reference.md)，但此基本示例 JavaScript 函数演示了如何访问传递给函数的请求对象中的属性，并对该属性值执行操作。 为访问对象内的属性，此示例使用[点 (.) 运算符](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)： 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

下面是此函数内部发生的情况：

1. 该函数创建 `data` 变量并将 `request` 对象内的 `body` 对象分配给该变量。 该函数使用点 (.) 运算符引用 `request` 对象内的 `body` 对象： 

   ```javascript
   var data = request.body;
   ```

2. 该函数现在可以通过 `data` 变量访问 `date` 属性，并通过调用 `ToDateString()` 函数将该属性值从 DateTime 类型转换为 DateString 类型。 该函数还通过函数响应中的 `body` 属性返回结果： 

   ```javascript
   body: data.date.ToDateString();
   ```

现已创建自己的 Azure 函数，请按步骤[将函数添加到逻辑应用](#add-function-logic-app)。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>在逻辑应用内部创建函数

若要在逻辑应用编辑器中从逻辑应用内部创建 Azure 函数，必须先具备一个 Azure 函数应用，这是函数的容器。 若没有函数应用，请先创建一个。 请参阅[在 Azure 门户中创建第一个函数](../azure-functions/functions-create-first-azure-function.md)。 

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的逻辑应用设计器中打开逻辑应用。 

2. 在要创建并添加函数的步骤下，选择“新建步骤” > “添加操作”。 

3. 在搜索框中输入“Azure Functions”作为筛选器。
从操作列表选择此操作：“选择 Azure 函数 - Azure Functions” 

   ![找到“Azure Functions”](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 从函数应用列表中选择自己的函数应用。 在操作列表打开后，选择此操作：“Azure Functions - 新建函数”

   ![选择函数应用](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. 在函数定义编辑器中定义函数：

   1. 在“函数名称”框中提供函数的名称。 

   2. 在“代码”框中，将函数代码添加至模板，包括函数运行完成后想返回至逻辑应用的响应和有效负载。 
   模板代码中的上下文对象描述了逻辑应用传递给函数的消息和内容，例如：

      ![定义函数](./media/logic-apps-azure-functions/function-definition.png)

      在函数中，可以使用此语法引用上下文对象中的属性：

      ```text
      context.<token-name>.<property-name>
      ```
      本示例中使用的语法如下所示：

      ```text
      context.body.content
      ```

   3. 完成后，选择“创建”。

6. 在“请求正文”框中，指定要作为函数输入传递的上下文对象，它必须以 JavaScript 对象表示法 (JSON) 进行格式化。 在“请求正文”框内单击时会打开动态内容列表，以便为上述步骤中的可用属性选择令牌。 

   此示例从电子邮件触发器传递“正文”令牌中的对象：  

   ![“请求正文”示例 - 上下文对象有效负载](./media/logic-apps-azure-functions/function-request-body-example.png)

   根据上下文对象中的内容，逻辑应用设计器会生成一个可进行内联编辑的函数模板。 
   逻辑应用还基于输入的上下文对象创建变量。

   在此示例中，上下文对象没有强制转换为字符串，因此内容将直接添加到 JSON 有效负载中。 
   但是如果该对象不是 JSON 令牌（它必须是字符串、JSON 对象或 JSON 数组），则会出现错误。 
   若要将上下文对象强制转换为字符串，请添加双引号，如下所示：

   ![将对象强制转换为字符串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. 若要指定其他详细信息，例如要使用的方法、请求标头或查询参数，请选择“显示高级选项”。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>将现有函数添加至逻辑应用

若要从逻辑应用调用现有 Azure 函数，可以添加 Azure 函数，具体方法与在逻辑应用设计器中执行的任何其他操作一样。 

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的逻辑应用设计器中打开逻辑应用。 

2. 在要添加函数的步骤下，选择“新建步骤” > “添加操作”。 

3. 在搜索框中输入“Azure Functions”作为筛选器。
从操作列表选择此操作：“选择 Azure 函数 - Azure Functions” 

   ![找到“Azure Functions”](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 从函数应用列表中选择自己的函数应用。 在显示的函数列表中选择函数。 

   ![选择函数应用和 Azure 函数](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   对于具备 API 定义（Swagger 描述）的函数以及那些[设置为可供逻辑应用查找和访问](#function-swagger)的函数，可以选择“Swagger 操作”:

   ![选择函数应用“Swagger 操作”以及 Azure 函数](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. 在“请求正文”框中，指定要作为函数输入传递的上下文对象，它必须以 JavaScript 对象表示法 (JSON) 进行格式化。 上下文对象描述逻辑应用发送至函数的消息和内容。 

   在“请求正文”框内单击时会打开动态内容列表，以便为上述步骤中的可用属性选择令牌。 
   此示例从电子邮件触发器传递“正文”令牌中的对象：

   ![“请求正文”示例 - 上下文对象有效负载](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此示例中，上下文对象没有强制转换为字符串，因此内容将直接添加到 JSON 有效负载中。 
   但是如果该对象不是 JSON 令牌（它必须是字符串、JSON 对象或 JSON 数组），则会出现错误。 
   若要将上下文对象强制转换为字符串，请添加双引号，如下所示：

   ![将对象强制转换为字符串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. 若要指定其他详细信息，例如要使用的方法、请求标头或查询参数，请选择“显示高级选项”。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>从函数调用逻辑应用

若要从 Azure 函数内部调用逻辑应用，该逻辑应用必须具备可调用的终结点，或者更具体地说，需要一个请求触发器。 然后从函数内部将 HTTP POST 请求发送至该请求触发器的 URL，其中包括想让逻辑应用处理的有效负载。 有关详细信息，请参阅[调用、触发器或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用连接器](../connectors/apis-list.md)
