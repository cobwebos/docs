---
title: 使用 HTTPS 接收和响应呼叫
description: 使用 Azure 逻辑应用处理来自外部服务的入站 HTTPS 请求
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
tags: connectors
ms.openlocfilehash: c6d8dc087e6306173fc4d55368cd3c4c624d5302
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978563"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>在 Azure 逻辑应用中接收和响应入站 HTTPS 请求

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置请求触发器和响应操作，你可以创建可接收和响应传入 HTTPS 请求的自动化任务和工作流。 例如，可以使用逻辑应用：

* 接收并响应对本地数据库中数据的 HTTPS 请求。
* 发生外部 Webhook 事件时触发工作流。
* 接收并响应来自另一个逻辑应用的 HTTPS 调用。

Request 触发器支持[Azure Active Directory 开放身份验证](../active-directory/develop/about-microsoft-identity-platform.md)（Azure AD OAuth）来授权对逻辑应用的入站调用。 有关启用此身份验证的详细信息，请参阅[Azure 逻辑应用中的安全访问和数据-启用 Azure AD OAuth 身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth)。

> [!NOTE]
> 对于传入呼叫，请求触发器仅支持** 传输层安全 (TLS) 1.2。 传出呼叫支持 TLS 1.0、1.1 和1.2。 有关详细信息，请参阅[解决 TLS 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)。
>
> 如果获取 TLS 握手错误，请确保使用 TLS 1.2。 
> 对于传入调用，以下是受支持的密码套件：
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-request"></a>

## <a name="add-request-trigger"></a>添加请求触发器

此内置触发器创建可手动调用的 HTTPS 终结点，该终结点只能接收** 传入的 HTTPS 请求。 发生此事件时，该触发器将会激发，并运行逻辑应用。 有关触发器的基础 JSON 定义以及如何调用此触发器的详细信息，请参阅[请求触发器类型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[在 Azure 逻辑应用中通过 HTTPS 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器打开后，在 "搜索" 框`http request`中，输入作为筛选器。 在触发器列表中，选择“收到 HTTP 请求时”触发器（逻辑应用工作流中的第一个步骤）。****

   ![选择请求触发器](./media/connectors-native-reqres/select-request-trigger.png)

   “请求”触发器显示以下属性：

   ![请求触发器](./media/connectors-native-reqres/request-trigger.png)

   | 属性名称 | JSON 属性名称 | 必需 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {无} | 是 | 保存逻辑应用后生成的终结点 URL，用于调用逻辑应用 |
   | **请求正文 JSON 架构** | `schema` | 否 | 描述传入请求正文中的属性和值的 JSON 架构 |
   |||||

1. 在“请求正文 JSON 架构”框中，有选择性地输入一个用于描述传入请求中的正文的 JSON 架构，例如：****

   ![示例 JSON 架构](./media/connectors-native-reqres/provide-json-schema.png)

   设计器将使用此架构为请求中的属性生成标记。 这样，逻辑应用就可以通过触发器分析、使用请求中的数据并将其传递到工作流。

   下面是示例架构：

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   输入 JSON 架构时，设计器将提醒你在请求中包含 `Content-Type` 标头，并将该标头值设置为 `application/json`。 有关详细信息，请参阅[处理内容类型](../logic-apps/logic-apps-content-type.md)。

   ![提醒包含“Content-Type”标头](./media/connectors-native-reqres/include-content-type.png)

   下面是此标头的 JSON 格式外观：

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   若要生成基于预期有效负载（数据）的 JSON 架构，可以使用 [JSONSchema.net](https://jsonschema.net) 之类的工具，也可以执行以下步骤：

   1. 在请求触发器中，选择“使用示例有效负载生成架构”。****

      ![基于有效负载生成架构](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 输入示例有效负载，然后选择“完成”。****

      ![基于有效负载生成架构](./media/connectors-native-reqres/enter-payload.png)

      下面是示例有效负载：

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. 若要添加其他属性，请打开“添加新参数”列表，并选择要添加的参数。****

   | 属性名称 | JSON 属性名称 | 必需 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 传入的请求在调用逻辑应用时必须使用的方法 |
   | **相对路径** | `relativePath` | 否 | 逻辑应用终结点 URL 可接受的参数的相对路径 |
   |||||

   本示例添加了 **Method** 属性：

   ![添加 Method 参数](./media/connectors-native-reqres/add-parameters.png)

   **Method** 属性显示在触发器中，使你可以从列表中选择方法。

   ![选择方法](./media/connectors-native-reqres/select-method.png)

1. 现在，添加另一个操作作为工作流中的下一步骤。 在触发器下，选择“下一步骤”，以便可以找到要添加的操作。****

   例如，可以通过[添加“响应”操作](#add-response)（可用于返回自定义的响应，本主题稍后将做介绍）来响应请求。

   逻辑应用只会使传入的请求保持打开一分钟。 假设逻辑应用工作流包含“响应”操作，如果在这段时间后逻辑应用未返回响应，则逻辑应用会向调用方返回 `504 GATEWAY TIMEOUT`。 否则，如果逻辑应用不包含“响应”操作，则逻辑应用会立即向调用方返回 `202 ACCEPTED` 响应。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”****。

   此步骤生成一个 URL，用于发送触发逻辑应用的请求。 若要复制此 URL，请选择 URL 旁边的复制图标。

   ![用于触发逻辑应用的 URL](./media/connectors-native-reqres/generated-url.png)

1. 若要触发逻辑应用，请将 HTTP POST 发送到生成的 URL。

   例如，你可以使用[Postman](https://www.getpostman.com/)之类的工具来发送 HTTP POST。 如果[启用 Azure Active Directory 打开身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth)（Azure AD OAuth）来授权对请求触发器的入站调用，请使用[共享访问签名（SAS） URL](../logic-apps/logic-apps-securing-a-logic-app.md#sas)或使用身份验证令牌来调用触发器，但不能同时使用这两者。 身份验证令牌必须在授权`Bearer`标头中指定类型。 有关详细信息，请参阅[Azure 逻辑应用中的安全访问和数据-访问基于请求的触发器](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers)。

有关触发器的基础 JSON 定义以及如何调用此触发器的详细信息，请参阅以下主题：[请求触发器类型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)以及[在 Azure 逻辑应用中通过 HTTP 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

### <a name="trigger-outputs"></a>触发器输出

下面是有关“请求”触发器的输出的详细信息：

| JSON 属性名称 | 数据类型 | 说明 |
|--------------------|-----------|-------------|
| `headers` | 对象 | 描述请求中的标头的 JSON 对象 |
| `body` | 对象 | 描述请求中的正文内容的 JSON 对象 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>添加“响应”操作

可以使用“响应”操作以有效负载（数据）响应传入的 HTTPS 请求，但只能在由 HTTPS 请求触发的逻辑应用中响应。 可以在工作流中的任意位置添加“响应”操作。 有关此触发器的基础 JSON 定义的详细信息，请参阅[“响应”操作类型](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)。

逻辑应用只会使传入的请求保持打开一分钟。 假设逻辑应用工作流包含“响应”操作，如果在这段时间后逻辑应用未返回响应，则逻辑应用会向调用方返回 `504 GATEWAY TIMEOUT`。 否则，如果逻辑应用不包含“响应”操作，则逻辑应用会立即向调用方返回 `202 ACCEPTED` 响应。

> [!IMPORTANT]
> 如果响应操作包含这些标头，则逻辑应用会从生成的响应消息中删除这些标头，而不会显示任何警告或错误：
>
> * `Allow`
> * `Content-*`但有以下例外`Content-Disposition`： `Content-Encoding`、和`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 尽管逻辑应用不会阻止你保存具有这些标头的响应操作的逻辑应用，但逻辑应用会忽略这些标头。

1. 在逻辑应用设计器中要添加“响应”操作的步骤下，选择“新建步骤”****。

   例如，使用前面所述的“请求”触发器：

   ![添加新步骤](./media/connectors-native-reqres/add-response.png)

   若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择出现的加号（**+**），然后选择 "**添加操作**"。

1. 在“选择操作”**** 下的搜索框中，输入“响应”作为筛选器，然后选择“响应”操作。****

   ![选择“响应”操作](./media/connectors-native-reqres/select-response-action.png)

   为简明起见，本示例中“请求”触发器已折叠。

1. 添加响应消息所需的任何值。 

   单击某些字段中的框会打开动态内容列表。 然后，可以从工作流中的前面步骤选择表示可用输出的标记。 在上述示例中指定的架构内的属性现在会显示在动态内容列表中。

   例如，对于“标头”框，请包含 `Content-Type` 作为键名称，并将键值设置为 `application/json`，如本主题前面所述。**** 对于“正文”框，可以从动态内容列表中选择触发器正文输出。****

   ![“响应”操作详细信息](./media/connectors-native-reqres/response-details.png)

   若要查看 JSON 格式的标头，请选择“切换到文本视图”。****

   ![标头 - 切换到文本视图](./media/connectors-native-reqres/switch-to-text-view.png)

   下面是有关可在“响应”操作中设置的属性的详细信息。 

   | 属性名称 | JSON 属性名称 | 必需 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **状态代码** | `statusCode` | 是 | 要在响应中返回的状态代码 |
   | **标头** | `headers` | 否 | 一个 JSON 对象，描述要包含在响应中的一个或多个标头 |
   | **大量** | `body` | 否 | 响应正文 |
   |||||

1. 若要添加其他属性，例如响应正文的 JSON 架构，请打开“添加新参数”列表，并选择要添加的参数。****

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”****。 

## <a name="next-steps"></a>后续步骤

* [适用于逻辑应用的连接器](../connectors/apis-list.md)