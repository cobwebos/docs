---
title: 使用 HTTPS 接收和响应调用
description: 使用 Azure 逻辑应用处理来自外部服务的入站 HTTPS 请求
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
tags: connectors
ms.openlocfilehash: 0dea516ea6b938b91fc4b9b833979bcecc285339
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714961"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>在 Azure 逻辑应用中接收和响应入站 HTTPS 请求

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置的请求触发器和响应操作，可以创建用于接收和响应传入的 HTTPS 请求的自动化任务和工作流。 例如，使用逻辑应用，你可以完成以下操作：

* 接收并响应对本地数据库中数据的 HTTPS 请求。
* 发生外部 Webhook 事件时触发工作流。
* 接收来自其他逻辑应用的 HTTPS 调用并对其作出响应。

请求触发器支持 [Azure Active Directory Open Authorization](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth)，用于授权对逻辑应用的入站调用。 有关如何启用此身份验证的详细信息，请参阅[保护 Azure 逻辑应用中的访问和数据安全 - 启用 Azure AD OAuth 身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth)。

> [!NOTE]
> 对于传入调用，请求触发器仅支持传输层安全性 (TLS) 1.2。 传出调用支持 TLS 1.0、1.1 和 1.2。 有关详细信息，请参阅[解决 TLS 1.0 问题](https://docs.microsoft.com/security/solving-tls1-problem)。
>
> 如果出现 TLS 握手错误，请确保使用 TLS 1.2。 
> 对于传入调用，下面是受支持的密码套件：
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

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基础知识。 如果你不熟悉逻辑应用，请了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-request"></a>

## <a name="add-request-trigger"></a>添加请求触发器

此内置触发器创建可手动调用的 HTTPS 终结点，该终结点仅可接收传入的 HTTPS 请求。 发生此事件时，触发器将触发并运行逻辑应用。 有关触发器的基础 JSON 定义以及如何调用此触发器的详细信息，请参阅[请求触发器类型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[通过 Azure 逻辑应用中的 HTTPS 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器打开后，在搜索框中，输入 `http request` 作为筛选器。 从触发器列表中，选择“当收到 HTTP 请求时”触发器，这是逻辑应用工作流中的第一步。

   ![选择请求触发器](./media/connectors-native-reqres/select-request-trigger.png)

   请求触发器显示以下属性：

   ![请求触发器](./media/connectors-native-reqres/request-trigger.png)

   | 属性名称 | JSON 属性名称 | 必选 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {无} | 是 | 保存逻辑应用后生成的终结点 URL，该 URL 用于调用逻辑应用 |
   | **请求正文 JSON 架构** | `schema` | 否 | JSON 架构，用于描述传入请求正文中的属性和值 |
   |||||

1. 在“请求正文 JSON 架构”框中，可以选择输入描述传入请求中的正文的 JSON 架构，例如：

   ![示例 JSON 架构](./media/connectors-native-reqres/provide-json-schema.png)

   设计器使用此架构为请求中的属性生成令牌。 这样一来，逻辑应用就可以分析、使用请求中的数据，并通过触发器将其传递到工作流中。

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

   输入 JSON 架构时，设计器会显示一个提醒，提醒在请求中包含 `Content-Type` 标头并将标头值设置为 `application/json`。 有关详细信息，请参阅[处理内容类型](../logic-apps/logic-apps-content-type.md)。

   ![提醒添加“Content-Type”标头](./media/connectors-native-reqres/include-content-type.png)

   下面显示的是采用 JSON 格式的标头：

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   要生成基于预期有效负载（数据）的 JSON 架构，可以使用 [JSONSchema.net](https://jsonschema.net) 等工具，也可以按照以下步骤操作：

   1. 在请求触发器中，选择“使用示例有效负载生成架构”。

      ![从有效负载生成架构](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 输入示例有效负载，然后选择“完成”。

      ![从有效负载生成架构](./media/connectors-native-reqres/enter-payload.png)

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

1. 要指定其他属性，请打开“添加新参数”列表，然后选择要添加的参数。

   | 属性名称 | JSON 属性名称 | 必选 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 一个方法，传入请求必须使用它才能调用逻辑应用 |
   | **Relative path** | `relativePath` | 否 | 逻辑应用的终结点 URL 可以接受的参数的相对路径 |
   |||||

   此示例添加了“Method”属性：

   ![添加 Method 参数](./media/connectors-native-reqres/add-parameters.png)

   “Method”属性会显示在触发器中，以便从列表中选择一种方法。

   ![选择方法](./media/connectors-native-reqres/select-method.png)

1. 现在，工作流中的下一步是添加其他操作。 在触发器下，选择“下一步”，以便可以找到要添加的操作。

   例如，可以通过[添加响应操作](#add-response)来响应请求，该响应操作可用于返回自定义响应，本主题后面部分将介绍此相关内容。

   逻辑应用仅在[有限的时间](../logic-apps/logic-apps-limits-and-config.md#request-limits)内使传入请求保持打开状态。 假设逻辑应用工作流包含响应操作，如果该逻辑应用在此时间之后没有返回响应，则逻辑应用会将 `504 GATEWAY TIMEOUT` 返回给调用方。 否则，如果逻辑应用不包含响应操作，则逻辑应用会立即将 `202 ACCEPTED` 响应返回给调用方。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”。

   此步骤将生成用于发送触发逻辑应用的请求的 URL。 要复制此 URL，请选择 URL 旁边的复制图标。

   ![用于触发逻辑应用的 URL](./media/connectors-native-reqres/generated-url.png)

1. 要触发逻辑应用，请向生成的 URL 发送一个 HTTP POST。

   例如，可以使用 [Postman](https://www.getpostman.com/) 等工具来发送 HTTP POST。 如果[启用了 Azure Active Directory Open Authorization](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure AD OAuth) 来授权对请求触发器的入站调用，则可以使用[共享访问签名 (SAS) URL](../logic-apps/logic-apps-securing-a-logic-app.md#sas) 或身份验证令牌来调用触发器（不能同时使用这两种方法）。 身份验证令牌必须在授权标头中指定 `Bearer` 类型。 有关详细信息，请参阅[保护 Azure 逻辑应用中的访问和数据安全 - 访问基于请求的触发器](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers)。

有关触发器的基础 JSON 定义以及如何调用此触发器的详细信息，请参阅以下主题：[请求触发器类型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[通过 Azure 逻辑应用中的 HTTP 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

### <a name="trigger-outputs"></a>触发器输出

下面是有关来自请求触发器的输出的详细信息：

| JSON 属性名称 | 数据类型 | 说明 |
|--------------------|-----------|-------------|
| `headers` | 对象 | 一个 JSON 对象，用于描述请求中的标头 |
| `body` | 对象 | 一个 JSON 对象，用于描述请求中的正文内容 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>添加响应操作

可以使用响应操作通过有效负载（数据）来响应传入的 HTTPS 请求，但只能在由 HTTPS 请求触发的逻辑应用中进行响应。 可以随时在工作流中添加响应操作。 有关此触发器的基础 JSON 定义的详细信息，请参阅[响应操作类型](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)。

逻辑应用仅在[有限的时间](../logic-apps/logic-apps-limits-and-config.md#request-limits)内使传入请求保持打开状态。 假设逻辑应用工作流包含响应操作，如果该逻辑应用在此时间之后没有返回响应，则逻辑应用会将 `504 GATEWAY TIMEOUT` 返回给调用方。 否则，如果逻辑应用不包含响应操作，则逻辑应用会立即将 `202 ACCEPTED` 响应返回给调用方。

> [!IMPORTANT]
> 如果响应操作包括这些标头，则逻辑应用会从生成的响应消息中删除这些标头，而不会显示任何警告或错误：
>
> * `Allow`
> * `Content-*`，但 `Content-Disposition`、`Content-Encoding` 和 `Content-Type` 除外
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 尽管逻辑应用不会阻止你保存具有带这些标头的响应操作的逻辑应用，但逻辑应用会忽略这些标头。

1. 在逻辑应用设计器中，在要添加响应操作的步骤下，选择“新步骤”。

   例如，使用前面的请求触发器：

   ![添加新步骤](./media/connectors-native-reqres/add-response.png)

   若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择出现的加号 (+)，然后选择“添加操作”。

1. 在“选择操作”下的搜索框中，输入“响应”作为筛选器，然后选择“响应”操作 。

   ![选择响应操作](./media/connectors-native-reqres/select-response-action.png)

   为了简单起见，此示例中折叠了请求触发器。

1. 添加响应消息所需的任何值。 

   在某些字段中，单击其框内部分将打开动态内容列表。 然后，可以选择代表工作流的先前步骤中可用输出的令牌。 前面示例中指定的架构的属性现在显示在动态内容列表中。

   例如，对于“标头”框，添加 `Content-Type` 作为键名，并将键值设置为 `application/json`，如本主题前面所述。 对于“正文”框，可以从动态内容列表中选择触发器正文输出。

   ![响应操作详细信息](./media/connectors-native-reqres/response-details.png)

   要查看 JSON 格式的标头，请选择“切换到文本视图”。

   ![标头 - 切换到文本视图](./media/connectors-native-reqres/switch-to-text-view.png)

   下面是有关可以在响应操作中设置的属性的详细信息。 

   | 属性名称 | JSON 属性名称 | 必选 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **状态代码** | `statusCode` | 是 | 要在响应中返回的状态代码 |
   | **标头** | `headers` | 否 | 一个 JSON 对象，用于描述要包含在响应中的一个或多个标头 |
   | **正文** | `body` | 否 | 响应正文 |
   |||||

1. 要指定其他属性（例如响应正文的 JSON 架构），请打开“添加新参数”列表，然后选择要添加的参数。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”。 

## <a name="next-steps"></a>后续步骤

* [适用于逻辑应用的连接器](../connectors/apis-list.md)
