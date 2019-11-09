---
title: 调用、触发或嵌套逻辑应用-Azure 逻辑应用
description: 设置 HTTP 终结点，以便在 Azure 逻辑应用中调用、触发或嵌套逻辑应用工作流
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: 27a3bf036cce27a5f215068ff71928cb7e181452
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833911"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 HTTP 终结点调用、触发或嵌套逻辑应用

若要使逻辑应用可通过 URL 调用，以便逻辑应用可以接收来自其他服务的传入请求，你可以在该逻辑应用上本机公开同步 HTTP 终结点作为触发器。 设置此功能时，还可以将逻辑应用嵌套在其他逻辑应用中，这使你可以创建可调用终结点的模式。

若要设置 HTTP 终结点，可以使用以下任何触发器类型，使逻辑应用能够接收传入的请求：

* [请求](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* 具有[ApiConnectionWebhook 类型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)并可接收传入 HTTP 请求的托管连接器触发器

> [!NOTE]
> 这些示例使用请求触发器，但你可以使用上一列表中的任何基于 HTTP 请求的触发器。 所有原则均相同适用于这些其他触发器类型。

如果你不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 要将 HTTP 终结点设置为触发器的逻辑应用。 你可以开始使用空白逻辑应用，也可以从现有逻辑应用开始，将当前触发器替换为该应用。 此示例以空白逻辑应用开始。

## <a name="create-a-callable-endpoint"></a>创建可调用终结点

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中创建并打开空白逻辑应用。

   此示例使用请求触发器，但你可以使用任何可接收传入 HTTP 请求的触发器。 所有原则均相同适用于这些触发器。 有关请求触发器的详细信息，请参阅[使用 Azure 逻辑应用接收和响应传入 HTTPS 调用](../connectors/connectors-native-reqres.md)。

1. 在搜索框下，选择 "**内置**"。 在搜索框中，输入 "`request`" 作为筛选器。 从 "触发器" 列表中，选择**接收 HTTP 请求的时间**。

   ![查找并选择请求触发器](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. （可选）在 "**请求正文 JSON 架构**" 框中，可以输入一个 JSON 架构，用于描述预计触发器接收的负载或数据。

   设计器使用此架构来生成表示触发器输出的令牌。 然后，可以轻松地在逻辑应用的工作流中引用这些输出。 详细了解[从 JSON 架构生成的标记](#generated-tokens)。

   对于此示例，请输入以下架构：

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![为请求操作提供 JSON 架构](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   或者，你可以通过提供示例负载来生成 JSON 架构：

   1. 在**请求**触发器中，选择 "**使用示例有效负载生成架构**"。

   1. 在 "**输入或粘贴示例 JSON 负载**" 框中，输入示例负载，例如：

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. 准备就绪后，选择 "**完成**"。

      "**请求正文 JSON 架构**" 框现在显示生成的架构。

1. 保存逻辑应用。

   " **HTTP POST 至此 url** " 框现在显示了其他服务可用于调用和触发逻辑应用的生成的回调 URL。 此 URL 在查询参数中包含用于身份验证的共享访问签名（SAS）密钥，例如：

   ![为终结点生成的回调 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   还可以从逻辑应用的 "**概述**" 窗格中获取 HTTP 终结点 URL。

   1. 在逻辑应用的菜单中，选择“概述”。

   1. 在 "**摘要**" 部分，选择 "**查看触发器历史记录**"。

      ![从 Azure 门户获取 HTTP 终结点 URL](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. 在 "**回调 url [POST]** " 下，复制 url：

      ![从 Azure 门户复制 HTTP 端点 URL](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      或者，可以通过执行以下调用获取 URL：

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>设置预期 HTTP 方法

默认情况下，“请求”触发器需要 HTTP POST 请求。 但是，您可以指定不同的方法，但只能指定一种方法。

1. 在请求触发器中，打开 "**添加新参数**" 列表，然后选择 "方法"，该**方法**将此属性添加到触发器。

   ![将 "Method" 属性添加到触发器](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. 从 "**方法**" 列表中，选择另一个触发器所需的方法。 也可以指定自定义方法。

   例如，选择 "**获取**" 方法，以便稍后可以测试 HTTP 终结点的 URL。

   ![选择要用于触发器的 HTTP 方法](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>接受终结点 URL 中的参数

如果希望终结点 URL 接受参数，请在触发器中指定相对路径。 还需要显式设置 HTTP 请求所需[的方法](#set-method)。

1. 在请求触发器中，打开 "**添加新参数**" 列表，然后选择 "**相对路径**"，将此属性添加到触发器。

   ![将 "相对路径" 属性添加到触发器](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. 在 "**相对路径**" 属性中，指定你希望 URL 接受的 JSON 架构中的参数的相对路径，例如 `address/{postalCode}`。

   ![指定参数的相对路径](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. 要使用参数，请查找逻辑应用并**将其添加**到逻辑应用。

   1. 在请求触发器下，选择 "**新建步骤**" > "**添加操作**"。

   1. 在 "**选择操作**" 下的 "搜索" 框中，输入 `response` 作为筛选器。

   1. 从 "操作" 列表中，选择 "**响应**" 操作。

1. 在响应操作的 "**正文**" 属性中，包含表示在触发器的相对路径中指定的参数的令牌。

   例如，假设您希望响应操作返回 `Postal Code: {postalCode}`。

   在 "**正文**" 属性中，输入尾随空格 `Postal Code: `。 从显示的 "动态内容" 列表中，选择 "**邮政编码**" 标记。

   ![将指定参数添加到响应正文](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **Body**属性现在包含所选参数：

   ![带参数的示例响应正文](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. 保存逻辑应用。

    现在，HTTP 终结点 URL 包含相对路径，例如：

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. 要测试 HTTP 终结点，请将更新的 URL 复制并粘贴到另一个浏览器窗口（但要将 `{postalCode}` 替换为 `123456`），并按 Enter。

   你的浏览器将显示以下文本： `Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>通过 HTTP 终结点调用逻辑应用

创建 HTTP 终结点后，可以通过将 HTTP `POST` 请求发送到终结点的完整 URL 来触发逻辑应用。 逻辑应用对直接访问终结点提供内置支持。

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>从架构生成的令牌

在请求触发器中提供 JSON 架构后，逻辑应用设计器将为该架构中的属性生成令牌。 然后，可以使用这些令牌通过逻辑应用工作流传递数据。

例如，如果将更多属性（如 `"suite"`）添加到 JSON 架构，则这些属性的令牌可供你在逻辑应用的后续步骤中使用。 下面是完整的 JSON 架构：

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>创建嵌套逻辑应用

通过添加可接收请求的其他逻辑应用，可以在逻辑应用中嵌套工作流。 若要包含这些逻辑应用，请执行以下步骤：

1. 在要调用另一个逻辑应用的步骤下，选择 "**新建步骤**" > "**添加操作**"。

1. 在 "**选择操作**" 下，选择 "**内置**"。 在搜索框中，输入 "`logic apps`" 作为筛选器。 从 "操作" 列表中，选择 "**选择逻辑应用工作流**"。

   ![在当前逻辑应用中嵌套逻辑应用](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   设计器将显示符合条件的逻辑应用，供你选择。

1. 选择要从当前逻辑应用调用的逻辑应用。

   ![选择要从当前逻辑应用调用的逻辑应用](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>引用传入请求中的内容

如果传入请求的内容类型为 `application/json`，则可以引用传入请求中的属性。 否则，此内容被视为可以传递给其他 Api 的单个二进制单元。 若要在逻辑应用的工作流中引用此内容，需要先转换该内容。

例如，如果要传递具有 `application/xml` 类型的内容，则可以使用[`@xpath()` 表达式](../logic-apps/workflow-definition-language-functions-reference.md#xpath)来执行 XPath 提取，或使用[`@json()` 表达式](../logic-apps/workflow-definition-language-functions-reference.md#json)将 XML 转换为 JSON。 详细了解如何使用受支持的[内容类型](../logic-apps/logic-apps-content-type.md)。

若要获取传入请求的输出，可以使用[`@triggerOutputs` 表达式](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)。 例如，假设您的输出如下所示：

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

若要专门访问 `body` 属性，可以使用[`@triggerBody()` 表达式](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)作为快捷方式。

## <a name="respond-to-requests"></a>对请求的响应

有时，你想要通过将内容返回给调用方来响应触发逻辑应用的某些请求。 若要构造响应的状态代码、标头和正文，请使用响应操作。 此操作可以出现在逻辑应用中的任何位置，而不仅仅是工作流的末尾。 如果逻辑应用不包括响应操作，HTTP 终结点将*立即*响应 "202 已**接受**" 状态。

为了使原始调用方成功获得响应，必须在[请求超时限制](./logic-apps-limits-and-config.md)内完成响应所需的所有步骤，除非触发的逻辑应用被称为嵌套逻辑应用。 如果在此限制内未返回响应，则传入请求会超时，并收到**408 客户端超时**响应。

对于嵌套逻辑应用，父逻辑应用会继续等待响应直到完成所有步骤，而不管需要多长时间。

### <a name="construct-the-response"></a>构造响应

在响应正文中，可以包含多个标头和任意类型的内容。 例如，此响应的标头指定响应的内容类型为 `application/json`，且正文包含 `town` 和 `postalCode` 属性的值，这些值基于本主题中前面介绍的请求触发器的 JSON 架构。

![为 HTTP 响应操作提供响应内容](./media/logic-apps-http-endpoint/content-for-response-action.png)

响应具有以下属性：

| 属性（显示） | 属性（JSON） | 说明 |
|--------------------|-----------------|-------------|
| **状态代码** | `statusCode` | 要在传入请求的响应中使用的 HTTP 状态代码。 该代码可以是以 2xx、4xx 或 5xx 开头的任何有效状态代码。 但是，不允许使用 3xx 状态代码。 |
| **标头** | `headers` | 要包括在响应中的一个或多个标头 |
| **正文** | `body` | 一个正文对象，可以是字符串、JSON 对象甚至是从上一步引用的二进制内容 |
||||

若要查看响应操作的 JSON 定义以及逻辑应用的完整 JSON 定义，请在逻辑应用设计器工具栏上选择 "**代码视图**"。

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>问题解答

#### <a name="q-what-about-url-security"></a>问：URL 的安全性如何？

**答**： Azure 通过使用[共享访问签名（SAS）](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)安全地生成逻辑应用回调 url。 此签名通过作为查询参数传递，必须先进行验证，然后才能运行逻辑应用。 Azure 使用每个逻辑应用的机密密钥、触发器名称和执行的操作的唯一组合生成签名。 因此，除非用户对机密逻辑应用密钥拥有访问权限，否则他们无法生成有效的签名。

> [!IMPORTANT]
> 对于生产和安全系统，强烈建议不要直接从浏览器调用逻辑应用，原因如下：
>
> * URL 中会出现共享访问密钥。
> * 由于 Azure 逻辑应用客户之间的共享域，因此无法管理安全内容策略。

#### <a name="q-can-i-configure-http-endpoints-further"></a>问：是否可以进一步配置 HTTP 终结点？

**答**：是的，HTTP 端点通过[Azure API 管理](../api-management/api-management-key-concepts.md)支持更高级的配置。 此服务还提供相应的功能，使你能够以一致的方式管理所有 API（包括逻辑应用）、设置自定义域名和使用其他身份验证方法等等，例如：

* [更改请求方法](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [更改请求的 URL 段](../api-management/api-management-transformation-policies.md#RewriteURL)
* 在[Azure 门户](https://portal.azure.com/)中设置 API 管理域
* 设置用于检查基本身份验证的策略。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 逻辑应用接收和响应传入的 HTTPS 调用](../connectors/connectors-native-reqres.md)