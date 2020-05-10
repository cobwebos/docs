---
title: 调用、触发或嵌套逻辑应用
description: 设置 HTTPS 终结点，以便在 Azure 逻辑应用中调用、触发或嵌套逻辑应用工作流
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 7f91d8eab2e7a29163dae5ae2a4d34792ddd0cb0
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005509"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用 HTTPS 终结点调用、触发或嵌套逻辑应用

若要使逻辑应用可通过 URL 调用，以便逻辑应用可以接收来自其他服务的传入请求，你可以在该逻辑应用上本机公开同步 HTTPS 终结点作为触发器。 设置此功能时，还可以将逻辑应用嵌套在其他逻辑应用的内部，以便创建可调用终结点的模式。

若要设置可调用的终结点，可以使用这些触发器类型中的任何一种，使逻辑应用能够接收传入的请求：

* [请求](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* 具有[ApiConnectionWebhook 类型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)并可接收传入 HTTPS 请求的托管连接器触发器

> [!NOTE]
> 这些示例使用请求触发器，但你可以使用上一列表中的任何基于 HTTPS 请求的触发器。 所有原则同样适用于其他这些触发器类型。

如果你不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 要在其中使用触发器来创建可调用终结点的逻辑应用。 可以从一个空白的逻辑应用着手，也可以从要在其中替换当前触发器的现有逻辑应用着手。 本示例以空白的逻辑应用着手。

## <a name="create-a-callable-endpoint"></a>创建可调用的终结点

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中创建并打开一个空白逻辑应用。

   此示例使用请求触发器，但你可以使用任何可接收传入 HTTPS 请求的触发器。 所有原则同样适用于这些触发器。 有关请求触发器的详细信息，请参阅[使用 Azure 逻辑应用接收和响应入站 HTTPS 调用](../connectors/connectors-native-reqres.md)。

1. 在搜索框下，选择“内置”。**** 在搜索框中，输入 `request` 作为筛选器。 从 "触发器" 列表中，选择**接收 HTTP 请求的时间**。

   ![找到并选择“请求”触发器](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. （可选）在“请求正文 JSON 架构”框中，可以输入一个 JSON 架构，用于描述预期该触发器要接收的有效负载或数据。****

   设计器将使用此架构来生成表示触发器输出的标记。 然后，可以在逻辑应用的整个工作流中轻松引用这些输出。 详细了解[从 JSON 架构生成的标记](#generated-tokens)。

   对于本示例，请输入以下架构：

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

   ![提供“请求”操作的 JSON 架构](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   或者，可以通过提供示例有效负载来生成 JSON 架构：

   1. 在**请求**触发器中，选择 "**使用示例有效负载生成架构**"。

   1. 在“输入或粘贴示例 JSON 有效负载”框中输入示例有效负载，例如：****

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

   1. 准备就绪后，选择“完成”。****

      “请求正文 JSON 架构”框现在会显示生成的架构。****

1. 保存逻辑应用。

   现在，" **HTTP POST url** " 框中显示了其他服务可用于调用和触发逻辑应用的生成的回调 url。 此 URL 包括指定用于身份验证的共享访问签名（SAS）密钥的查询参数。

   ![为终结点生成的回调 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. 若要复制回调 URL，可以使用以下选项：

   * 在 " **HTTP POST url** " 框的右侧，选择 "**复制 URL** （复制文件" 图标）。

   * 发出此 POST 呼叫：

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * 从逻辑应用的 "**概述**" 窗格中复制回调 URL。

     1. 在逻辑应用的菜单中，选择“概述”****。

     1. 在“摘要”部分，选择“查看触发器历史记录”。********

        ![从 Azure 门户获取终结点 URL](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. 在“回调 URL [POST]”下，复制 URL：****

        ![从 Azure 门户复制终结点 URL](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>选择所需的请求方法

默认情况下，请求触发器需要 POST 请求。 您可以指定要使用的其他方法，但只能指定一个方法。

1. 在“请求”触发器中打开“添加新参数”列表，然后选择“方法”，将此属性添加到触发器中。********

   ![将“方法”属性添加到触发器中](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. 从 "**方法**" 列表中，选择触发器应采用的方法。 或者，可以指定自定义方法。

   例如，选择**GET**方法，以便以后可以测试终结点的 URL。

   ![选择触发器所需的请求方法](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>通过终结点 URL 传递参数

如果希望通过终结点的 URL 接受参数值，可以使用以下选项：

* [通过 GET 参数](#get-parameters)或 URL 参数接受值。

  这些值在终结点的 URL 中作为名称/值对传递。 对于此选项，需要在请求触发器中使用 GET 方法。 在后续操作中，可以通过在表达式中使用`triggerOutputs()`函数，将参数值作为触发器输出来获取。

* 通过请求触发器中参数的[相对路径接受值](#relative-path)。

  这些值是通过终结点 URL 中的相对路径传递的。 还需要显式选择触发器需要的[方法](#select-method)。 在后续操作中，可以通过直接引用这些输出，将参数值作为触发器输出来获取。

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>通过 GET 参数接受值

1. 在请求触发器中，打开 "**添加新参数" 列表**，将 "**方法**" 属性添加到触发器，然后选择 "**获取**" 方法。

   有关详细信息，请参阅[选择预期的请求方法](#select-method)。

1. 在请求触发器下，添加要在其中使用参数值的操作。 对于本示例，请添加 "**响应**" 操作。

   1. 在请求触发器下，选择 "**新建步骤** > " "**添加操作**"。
   
   1. 在“选择操作”**** 下的搜索框中，输入 `response` 作为筛选器。 在操作列表中选择“响应”操作。****

1. 若要生成`triggerOutputs()`检索参数值的表达式，请执行以下步骤：

   1. 单击 "响应" 操作的 "**正文**" 属性内部，以便显示 "动态内容" 列表，然后选择 "**表达式**"。

   1. 在 "**表达式**" 框中，输入此表达式`parameter-name` ，将替换为您的参数名称，然后选择 **"确定"**。

      `triggerOutputs()['queries']['parameter-name']`

      ![添加要触发的 "triggerOutputs （）" 表达式](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      在 "**正文**" 属性中，表达式解析为`triggerOutputs()`标记。

      ![解析的 "triggerOutputs （）" 表达式](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      如果保存逻辑应用，请从设计器导航到该设计器，然后返回到设计器，该令牌将显示您指定的参数名称，例如：

      ![已解析参数名称的表达式](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      在 "代码" 视图中，**主体**属性将显示在响应操作的定义中，如下所示：

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      例如，假设要传递名`postalCode`为的参数的值。 **Body**属性指定`Postal Code: `带有尾随空格的字符串，后跟相应的表达式：

      ![添加要触发的 "triggerOutputs （）" 表达式](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. 若要测试可调用终结点，请从请求触发器中复制回调 URL，并将 URL 粘贴到另一个浏览器窗口中。 在 URL 中，按以下格式将问号（`?`）后面的参数名称和值添加到 URL，然后按 enter。

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   浏览器将返回具有以下文本的响应：`Postal Code: 123456`

   ![向回调 URL 发送请求时的响应](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. 若要在 URL 内将参数名称和值放在不同的位置，请确保使用 "and"`&`符（）作为前缀，例如：

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   此示例显示了在 URL 中的不同位置包含示例参数`postalCode=123456`名称和值的回调 URL：

   * 第一个位置：`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * 第二个位置：`https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>通过相对路径接受值

1. 在“请求”触发器中打开“添加新参数”列表，然后选择“相对路径”，将此属性添加到触发器中。********

   ![将“相对路径”属性添加到触发器中](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. 在“相对路径”属性中，指定希望 URL 接受的 JSON 架构中参数的相对路径，例如 `/address/{postalCode}`。****

   ![指定参数的相对路径](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. 在请求触发器下，添加要在其中使用参数值的操作。 对于本示例，请添加 "**响应**" 操作。

   1. 在请求触发器下，选择 "**新建步骤** > " "**添加操作**"。

   1. 在“选择操作”**** 下的搜索框中，输入 `response` 作为筛选器。 在操作列表中选择“响应”操作。****

1. 在响应操作的“正文”属性中，包含表示在触发器相对路径中指定的参数的标记。****

   例如，假设你希望“响应”操作返回 `Postal Code: {postalCode}`。

   1. 请在“正文”属性中，输入 `Postal Code: ` 和一个尾部空格。**** 将光标停留在编辑框内，使动态内容列表保持打开状态。

   1. 在 "动态内容" 列表中，从 "**收到 HTTP 请求时**" 部分选择 "**邮政编码**" 标记。

      ![将指定的参数添加到响应正文](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      “正文”属性现在包含选定的参数：****

      ![包含参数的示例响应正文](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. 保存逻辑应用。

   在请求触发器中，回调 URL 已更新，现在包含相对路径，例如：

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. 若要测试可调用终结点，请从请求触发器复制更新的回调 URL，将 URL 粘贴到另一个浏览`{postalCode}`器窗口中， `123456`将 url 中的替换为，然后按 enter。

   浏览器将返回具有以下文本的响应：`Postal Code: 123456`

   ![向回调 URL 发送请求时的响应](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

## <a name="call-logic-app-through-endpoint-url"></a>通过终结点 URL 调用逻辑应用

创建终结点后，可以通过将 HTTPS `POST`请求发送到终结点的完整 URL 来触发逻辑应用。 逻辑应用对直接访问终结点提供内置支持。

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>从架构生成的标记

在“请求”触发器中提供 JSON 架构时，逻辑应用设计器会在该架构中生成属性的标记。 然后，可以使用这些令牌通过逻辑应用工作流传递数据。

例如，如果将其他属性（例如 `"suite"`）添加到 JSON 架构，则可以在逻辑应用的后续步骤中使用这些属性的标记。 下面是完整的 JSON 架构：

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

## <a name="create-nested-logic-apps"></a>创建嵌套的逻辑应用

通过添加可接收请求的其他逻辑应用，可以在逻辑应用中嵌套工作流。 若要包含这些逻辑应用，请执行以下步骤：

1. 在要调用另一个逻辑应用的步骤下，选择 "**新建步骤** > " "**添加操作**"。

1. 在“选择操作”下，选择“内置”。******** 在搜索框中，输入 `logic apps` 作为筛选器。 在操作列表中，选择“选择逻辑应用工作流”。****

   ![在当前逻辑应用内部嵌套逻辑应用](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   设计器将显示可供选择的逻辑应用。

1. 选择要从当前逻辑应用调用的逻辑应用。

   ![选择要从当前逻辑应用调用的逻辑应用](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>引用传入请求中的内容

如果传入请求的内容类型为 `application/json`，则可以在传入请求中引用属性。 否则，此内容被视为可以传递给其他 API 的单个二进制单元。 若要在逻辑应用的工作流内部引用此内容，首先需要转换此内容。

例如，如果要传递具有`application/xml`类型的内容，则可以使用[ `@xpath()`表达式](../logic-apps/workflow-definition-language-functions-reference.md#xpath)来执行 XPath 提取，或使用[ `@json()`表达式](../logic-apps/workflow-definition-language-functions-reference.md#json)将 XML 转换为 JSON。 详细了解如何处理支持的[内容类型](../logic-apps/logic-apps-content-type.md)。

若要获取传入请求的输出，可以使用[ `@triggerOutputs`表达式](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)。 例如，假设输出如以下示例所示：

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

若要专门访问`body`属性，可以将[ `@triggerBody()`表达式](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)用作快捷方式。

## <a name="respond-to-requests"></a>对请求的响应

有时，你希望通过向调用方返回内容，对触发逻辑应用的某些请求做出响应。 若要构造响应的状态代码、标头和正文，请使用“响应”操作。 此操作可以出现在逻辑应用中的任何位置，而不仅仅是工作流的末尾。 如果逻辑应用不包括响应操作，则终结点将*立即*响应 " **202 已接受**" 状态。

要使原始调用方能够成功获取响应，除非调用用作嵌套逻辑应用的已触发逻辑应用，否则响应所需的所有步骤必须在[请求超时限制](./logic-apps-limits-and-config.md)内完成。 如果在此限制时间内未返回响应，传入请求将会超时，并收到“408 客户端超时”响应。****

对于嵌套逻辑应用，父逻辑应用会继续等待响应，直到所有步骤已完成，而不考虑所需的时间。

### <a name="construct-the-response"></a>构造响应

在响应正文中，可以包含多个标头和任意类型的内容。 例如，此响应的标头指定响应的内容类型为 `application/json`，并指定正文包含 `town` 和 `postalCode` 属性的值，这些值基于本主题前面所述的“请求”触发器的 JSON 架构。

![为 HTTPS 响应操作提供响应内容](./media/logic-apps-http-endpoint/content-for-response-action.png)

响应具有以下属性：

| 属性（显示值） | 属性 (JSON) | 说明 |
|--------------------|-----------------|-------------|
| **状态代码** | `statusCode` | 要在传入请求的响应中使用的 HTTPS 状态代码。 该代码可以是以 2xx、4xx 或 5xx 开头的任何有效状态代码。 但是，不允许使用 3xx 状态代码。 |
| **标头** | `headers` | 要包含在响应中的一个或多个标头 |
| **正文** | `body` | 正文对象，可以是字符串、JSON 对象甚至是从上一步引用的二进制内容 |
||||

若要查看“响应”操作的 JSON 定义以及逻辑应用的完整 JSON 定义，请在逻辑应用设计器工具栏上选择“代码视图”。****

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

## <a name="q--a"></a>问与答

#### <a name="q-what-about-url-security"></a>问：URL 的安全性如何？

**答**： Azure 通过使用[共享访问签名（SAS）](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)安全地生成逻辑应用回调 url。 此签名以查询参数的形式传递，在运行逻辑应用之前必须先验证此签名。 Azure 使用每个逻辑应用的机密密钥、触发器名称和执行的操作的唯一组合生成签名。 因此，除非用户对机密逻辑应用密钥拥有访问权限，否则他们无法生成有效的签名。

> [!IMPORTANT]
> 对于生产和更高版本的安全系统，强烈建议不要直接从浏览器调用逻辑应用，原因如下：
>
> * URL 中会出现共享访问密钥。
> * 由于 Azure 逻辑应用客户之间的共享域，因此无法管理安全内容策略。

#### <a name="q-can-i-configure-callable-endpoints-further"></a>问：是否可以进一步配置可调用终结点？

**答**：是的，HTTPS 终结点通过[Azure API 管理](../api-management/api-management-key-concepts.md)支持更高级的配置。 此服务还提供相应的功能，使你能够以一致的方式管理所有 API（包括逻辑应用）、设置自定义域名和使用其他身份验证方法等等，例如：

* [更改请求方法](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [更改请求的 URL 段](../api-management/api-management-transformation-policies.md#RewriteURL)
* 在 [Azure 门户](https://portal.azure.com/)中设置 API 管理域
* 设置用于检查基本身份验证的策略。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 逻辑应用接收和响应传入的 HTTPS 调用](../connectors/connectors-native-reqres.md)
