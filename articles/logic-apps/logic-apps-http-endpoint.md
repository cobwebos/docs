---
title: "使用 HTTP 终结点调用、触发或嵌套工作流 - Azure 逻辑应用 | Microsoft Docs"
description: "设置 HTTP 终结点用于调用、触发或嵌套 Azure 逻辑应用的工作流"
services: logic-apps
keywords: "工作流, HTTP 终结点"
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: de4f4ee086fbf3799fcac1f1b008d9237b5e7a09
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>在逻辑应用中使用 HTTP 终结点调用、触发或嵌套工作流

可以在逻辑应用中以触发器的形式本机公开同步的 HTTP 终结点，以便通过 URL 触发或调用逻辑应用。 还可以通过可调用的终结点模式在逻辑应用中嵌套工作流。

若要创建 HTTP 终结点，可以添加以下触发器，使逻辑应用能够接收传入的请求：

* [请求](../connectors/connectors-native-reqres.md)

* [API 连接 Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > 尽管这些示例使用“请求”触发器，但也可使用任何所列的 HTTP 触发器，所有原理同样适用于其他触发器类型。

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>设置逻辑应用的 HTTP 终结点

若要创建 HTTP 终结点，请添加一个可以接收传入请求的触发器。

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。 转到逻辑应用，并打开逻辑应用设计器。

2. 添加可让逻辑应用接收传入请求的触发器。 例如，将“请求”触发器添加到逻辑应用。

3.  在“请求正文 JSON 架构”下面，可以选择性地输入期望该触发器接收的有效负载（数据）的 JSON 架构。

    设计器使用此架构生成可让逻辑应用通过工作流使用、分析数据并从触发器传递数据的令牌。 
    有关[从 JSON 架构生成的令牌](#generated-tokens)的详细信息。

    对于本示例，请输入设计器中显示的架构：

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![添加请求操作][1]

    > [!TIP]
    > 
    > 可以通过 [jsonschema.net](http://jsonschema.net/) 等工具为示例 JSON 有效负载生成架构，也可以选择“使用示例有效负载生成架构”，在“请求”触发器中生成该架构。 
    > 输入示例有效负载，并选择“完成”。

    例如，此示例有效负载：

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    生成此架构：

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  保存逻辑应用。 在“指向此 URL 的 HTTP POST”下面，应会显示一个生成的回调 URL，如以下示例所示：

    ![为终结点生成的回调 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    此 URL 在查询参数中包含用于身份验证的共享访问签名 (SAS) 密钥。 
    也可以通过 Azure 门户中的逻辑应用概述获取 HTTP 终结点 URL。 在“触发器历史记录”下面选择触发器：

    ![从 Azure 门户获取 HTTP 终结点 URL][2]

    或者，可以通过执行以下调用获取 URL：

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>更改触发器的 HTTP 方法

默认情况下，“请求”触发器需要 HTTP POST 请求，但可以使用不同的 HTTP 方法。 

> [!NOTE]
> 只能指定一个方法类型。

1. 在“请求”触发器上，选择“显示高级选项”。

2. 打开“方法”列表。 对于本示例，请选择“GET”，以便稍后可以测试 HTTP 终结点的 URL。

    > [!NOTE]
    > 可以选择其他任何 HTTP 方法，或者为自己的逻辑应用指定自定义方法。

    ![更改 HTTP 方法](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>通过 HTTP 终结点 URL 接受参数

如果希望 HTTP 终结点 URL 接受参数，请自定义触发器的相对路径。

1. 在“请求”触发器上，选择“显示高级选项”。 

2. 在“方法”下面，指定希望请求使用的 HTTP 方法。 对于本示例，请选择“GET”方法（如果尚未选择），以便可以测试 HTTP 终结点的 URL。

      > [!NOTE]
      > 指定触发器的相对路径时，还必须显式指定触发器的 HTTP 方法。

3. 在“相对路径”下面，指定 URL 应接受的参数的相对路径，例如 `customers/{customerID}`。

    ![指定 HTTP 方法以及参数的相对路径](./media/logic-apps-http-endpoint/relativeurl.png)

4. 要使用参数，请将“响应”操作添加到逻辑应用。 （在触发器下面，选择“新建步骤” > “添加操作” > “响应”） 

5. 在响应的“正文”下面，包含触发器相对路径中指定的参数的令牌。

    例如，若要返回 `Hello {customerID}`，请使用 `Hello {customerID token}` 更新响应的**正文**。 
    此时应会显示动态内容列表，其中显示了 `customerID` 令牌供你选择。

    ![将参数添加到响应正文](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    **正文**应如以下示例所示：

    ![包含参数的响应正文](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. 保存逻辑应用。 

    现在，HTTP 终结点 URL 包含相对路径，例如： 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. 要测试 HTTP 终结点，请将更新的 URL 复制并粘贴到另一个浏览器窗口（但要将 `{customerID}` 替换为 `123456`），然后按 Enter。

    浏览器应显示以下文本： 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>从 JSON 架构为逻辑应用生成的令牌

在“请求”触发器中提供 JSON 架构时，逻辑应用设计器会在该架构中生成属性的令牌。 然后，可以使用这些令牌通过逻辑应用工作流传递数据。

例如，如果将 `title` 和 `name` 属性添加到 JSON 架构，这些属性的令牌现可用于后续工作流步骤。 

下面是完整的 JSON 架构：

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>为逻辑应用创建嵌套工作流

通过添加可接收请求的其他逻辑应用，可以在逻辑应用中嵌套工作流。 要包含这些逻辑应用，请将“Azure 逻辑应用 - 选择逻辑应用工作流”操作添加到触发器。 然后，可以从符合条件的逻辑应用中选择。

![添加另一个逻辑应用](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>通过 HTTP 终结点调用或触发逻辑应用

创建 HTTP 终结点之后，可以通过针对整个 URL 执行 `POST` 方法来触发逻辑应用。 逻辑应用对直接访问终结点提供内置支持。

> [!NOTE] 
> 在任何时候若要手动运行逻辑应用，请在逻辑应用设计器或逻辑应用代码视图工具栏上，选择“运行”。

## <a name="reference-content-from-an-incoming-request"></a>引用传入请求中的内容

如果内容的类型为 `application/json`，可以引用传入请求中的属性。 否则，内容被视为可以传递给其他 API 的单个二进制单元。 若要引用工作流中的此内容，则必须转换此内容。 例如，如果传递 `application/xml` 内容，可以使用 `@xpath()` 执行 XPath 提取，或使用 `@json()` 将 XML 转换为 JSON。 了解[处理内容类型](../logic-apps/logic-apps-content-type.md)。

若要从传入请求中获取输出，可以使用 `@triggerOutputs()` 函数。 输出可能如以下示例所示：

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

若要专门访问 `body` 属性，可使用 `@triggerBody()` 快捷方式。 

## <a name="respond-to-requests"></a>对请求的响应

可能想要通过向调用方返回内容，对启动逻辑应用的某些请求做出响应。 若要构造响应的状态代码、标头和正文，可以使用“响应”操作。 此操作可以出现在逻辑应用中的任何位置，而不仅仅是工作流的末尾。

> [!NOTE] 
> 如果逻辑应用不包含“响应”，HTTP 终结点会*立即*响应“202 已接受”状态。 另外，要使原始请求能够获取响应，除非调用用作嵌套逻辑应用的工作流，否则响应所需的所有步骤必须在[请求超时限制](./logic-apps-limits-and-config.md)内完成。 如果在此限制时间内未发生任何响应，传入请求会超时，并收到 HTTP 响应“408 客户端超时”。 对于嵌套逻辑应用，父逻辑应用会继续等待响应，直到完成为止（不考虑所需的时间）。

### <a name="construct-the-response"></a>构造响应

可以在响应正文中包含多个标头和任何类型的内容。 在示例响应中，标头指定响应的内容类型为 `application/json`。 正文根据前面为“请求”触发器更新的 JSON 架构包含 `title` 和 `name`。

![HTTP 响应操作][3]

响应具有以下属性：

| 属性 | 说明 |
| --- | --- |
| statusCode |指定用于响应传入请求的 HTTP 状态代码。 该代码可以是以 2xx、4xx 或 5xx 开头的任何有效状态代码。 但是，不允许使用 3xx 状态代码。 |
| headers |定义要包含在响应中的任意数目的标头。 |
| body |指定正文对象，可以是字符串、JSON 对象甚至是从上一步引用的二进制内容。 |

下面是“响应”操作的 JSON 架构的当前外观：

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> 若要查看逻辑应用的完整 JSON 定义，请在逻辑应用设计器中选择“代码视图”。

## <a name="q--a"></a>问题解答

#### <a name="q-what-about-url-security"></a>问：URL 的安全性如何？

答：Azure 使用共享访问签名 (SAS) 安全生成逻辑应用回调 URL。 此签名以查询参数的形式传递。在触发逻辑应用之前必须验证此签名。 Azure 使用每个逻辑应用的机密密钥、触发器名称和执行的操作的唯一组合生成签名。 因此，除非用户对机密逻辑应用密钥拥有访问权限，否则他们无法生成有效的签名。

   > [!IMPORTANT]
   > 对于生产和安全系统，强烈建议不要直接从浏览器中调用逻辑应用，因为：
   > 
   > * URL 中会出现共享访问密钥。
   > * 由于跨逻辑应用客户的共享域，将无法管理安全内容策略。

#### <a name="q-can-i-configure-http-endpoints-further"></a>问：是否可以进一步配置 HTTP 终结点？

答：可以，HTTP 终结点支持通过 [**API 管理**](../api-management/api-management-key-concepts.md)进行更高级的配置。 此服务还提供相应的功能，使你能够以一致的方式管理所有 API（包括逻辑应用）、设置自定义域名和使用其他身份验证方法等等，例如：

* [更改请求方法](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [更改请求的 URL 段](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* 在 [Azure 门户](https://portal.azure.com/ "Azure 门户")中设置 API 管理域
* 设置用于检查基本身份验证的策略。

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>问：从 2014 年 12 月 1 日预览版迁移的架构发生了哪些更改？

答：下面是有关更改的摘要：

| 2014 年 12 月 1 日预览版 | 2016 年 6 月 1 日 |
| --- | --- |
| 单击“HTTP 侦听器”API 应用 |单击“手动触发器”（无需 API 应用） |
| HTTP 侦听器设置“自动发送响应” |在工作流定义中包含或不包含“响应”操作 |
| 配置基本或 OAuth 身份验证 |通过 API 管理 |
| 配置 HTTP 方法 |在“显示高级选项”下面，选择一个 HTTP 方法 |
| 配置相对路径 |在“显示高级选项”下面，添加一个相对路径 |
| 通过 `@triggerOutputs().body.Content` 引用传入正文 |通过 `@triggerOutputs().body` 引用 |
| 对 HTTP 侦听器执行的“发送 HTTP 响应”操作 |单击“响应 HTTP 请求”（无需 API 应用） |

## <a name="get-help"></a>获取帮助

若要提问、解答问题和了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

* [编写逻辑应用定义](./logic-apps-author-definitions.md)
* [处理错误和异常](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png