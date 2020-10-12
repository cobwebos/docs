---
title: 使用 HTTPS 接收和响应调用
description: 使用 Azure 逻辑应用处理来自外部服务的入站 HTTPS 请求
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 05ce944d195cf43f860fc2b39975a736a4454c05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89226508"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>在 Azure 逻辑应用中接收和响应入站 HTTPS 请求

通过 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 和内置请求触发器和响应操作，你可以创建可通过 HTTPS 接收入站请求的自动化任务和工作流。 若要改为发送出站请求，请使用内置 [HTTP 触发器或 http 操作](../connectors/connectors-native-http.md)。

例如，使用逻辑应用，你可以完成以下操作：

* 接收并响应对本地数据库中数据的 HTTPS 请求。

* 发生外部 Webhook 事件时触发工作流。

* 接收来自其他逻辑应用的 HTTPS 调用并对其作出响应。

本文介绍如何使用 "请求触发器和响应" 操作，以便逻辑应用可以接收和响应入站调用。

有关对逻辑应用的入站调用的加密、安全和授权（例如 [传输层安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)，之前称为安全套接字层 (SSL) ），或 [Azure Active Directory 开放式身份验证 (Azure AD OAuth) ](../active-directory/develop/index.yml)，请参阅 [对基于请求的触发器的入站调用的安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

<a name="add-request"></a>

## <a name="add-request-trigger"></a>添加请求触发器

此内置触发器创建 *只能处理 HTTPS* 上的入站请求的手动可调用终结点。 当调用方将请求发送到此终结点时， [请求触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) 将触发并运行逻辑应用。 有关如何调用此触发器的详细信息，请参阅 [在 Azure 逻辑应用中通过 HTTPS 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

逻辑应用使入站请求仅在 [有限时间](../logic-apps/logic-apps-limits-and-config.md#request-limits)打开。 假定逻辑应用包含 [响应操作](#add-response)，如果逻辑应用在这段时间过后未向调用方发送响应，则逻辑应用会 `504 GATEWAY TIMEOUT` 向调用方返回状态。 如果逻辑应用不包括响应操作， 
> 逻辑应用会立即将 `202 ACCEPTED` 状态返回给调用方。

1. 登录 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器打开后，在搜索框中，输入 `http request` 作为筛选器。 从 "触发器" 列表中，选择 " **收到 HTTP 请求时** " 触发器。

   ![选择请求触发器](./media/connectors-native-reqres/select-request-trigger.png)

   请求触发器显示以下属性：

   ![请求触发器](./media/connectors-native-reqres/request-trigger.png)

   | 属性名称 | JSON 属性名称 | 必须 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {无} | 是 | 保存逻辑应用后生成的终结点 URL，用于调用逻辑应用 |
   | **请求正文 JSON 架构** | `schema` | 否 | 描述传入请求正文中的属性和值的 JSON 架构 |
   |||||

1. 在“请求正文 JSON 架构”框中，有选择性地输入一个用于描述传入请求中的正文的 JSON 架构，例如：

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

   1. 在请求触发器中，选择“使用示例有效负载生成架构”。

      ![选择了 "使用示例有效负载生成架构" 的屏幕截图](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 输入示例有效负载，然后选择“完成”。

      ![输入用于生成架构的示例负载](./media/connectors-native-reqres/enter-payload.png)

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

1. 若要检查入站调用是否具有与指定架构匹配的请求正文，请执行以下步骤：

   1. 在请求触发器的标题栏中，选择省略号按钮 ( **...** )。

   1. 在触发器的设置中，开启“架构验证”，然后选择“完成”。

      如果入站调用的请求正文与架构不匹配，则触发器会返回“`HTTP 400 Bad Request`”错误。

1. 若要添加其他属性，请打开“添加新参数”列表，并选择要添加的参数。

   | 属性名称 | JSON 属性名称 | 必须 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 传入的请求在调用逻辑应用时必须使用的方法 |
   | **相对路径** | `relativePath` | 否 | 逻辑应用终结点 URL 可接受的参数的相对路径 |
   |||||

   本示例添加了 **Method** 属性：

   ![添加 Method 参数](./media/connectors-native-reqres/add-parameters.png)

   **Method** 属性显示在触发器中，使你可以从列表中选择方法。

   ![选择方法](./media/connectors-native-reqres/select-method.png)

1. 现在，添加另一个操作作为工作流中的下一步骤。 在触发器下，选择“下一步骤”，以便可以找到要添加的操作。

   例如，可以通过[添加响应操作](#add-response)来响应请求，该响应操作可用于返回自定义响应，本主题后面部分将介绍此相关内容。

   逻辑应用仅在[有限的时间](../logic-apps/logic-apps-limits-and-config.md#request-limits)内使传入请求保持打开状态。 假设逻辑应用工作流包含响应操作，如果该逻辑应用在此时间之后没有返回响应，则逻辑应用会将 `504 GATEWAY TIMEOUT` 返回给调用方。 否则，如果逻辑应用不包含响应操作，则逻辑应用会立即将 `202 ACCEPTED` 响应返回给调用方。

1. 完成后，保存逻辑应用。 在设计器工具栏上选择“保存”。

   此步骤生成一个 URL，用于发送触发逻辑应用的请求。 若要复制此 URL，请选择 URL 旁边的复制图标。

   ![用于触发逻辑应用的 URL](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > 若要在调用请求触发器时在 URI 中包含哈希符号或井号（“#”），请改用此编码版本：`%25%23`

1. 若要触发逻辑应用，请将 HTTP POST 发送到生成的 URL。

   例如，可以使用 [Postman](https://www.getpostman.com/) 等工具来发送 HTTP POST。 有关触发器的基础 JSON 定义以及如何调用此触发器的详细信息，请参阅以下主题：[请求触发器类型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[通过 Azure 逻辑应用中的 HTTP 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

## <a name="trigger-outputs"></a>触发器输出

下面是有关“请求”触发器的输出的详细信息：

| JSON 属性名称 | 数据类型 | 说明 |
|--------------------|-----------|-------------|
| `headers` | 对象 | 描述请求中的标头的 JSON 对象 |
| `body` | 对象 | 描述请求中的正文内容的 JSON 对象 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>添加响应操作

使用请求触发器处理入站请求时，可以使用内置 [响应操作](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)对响应进行建模并将负载结果发送回调用方。 *只能将响应操作用于*请求触发器。 这种与 "请求触发器" 和 "响应" 操作的结合会创建 [请求-响应模式](https://en.wikipedia.org/wiki/Request%E2%80%93response)。 除了 Foreach 循环和循环之间以及并行分支以外，还可以在工作流中的任意位置添加响应操作。

> [!IMPORTANT]
> 如果响应操作包含这些标头，则逻辑应用会从生成的响应消息中删除这些标头，且不显示任何警告或错误：
>
> * `Allow`
> * `Content-*` 中含以下例外：`Content-Disposition`、`Content-Encoding` 和 `Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 尽管逻辑应用不会阻止保存具有这些标头的响应操作的逻辑应用，但逻辑应用会忽略这些标头。

1. 在逻辑应用设计器中，在要添加响应操作的步骤下，选择“新步骤”。

   例如，使用前面所述的“请求”触发器：

   ![添加新步骤](./media/connectors-native-reqres/add-response.png)

   若要在步骤之间添加操作，请将鼠标指针移到这些步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在 " **选择操作**" 下的 "搜索" 框中，输入 `response` 作为筛选器，然后选择 " **响应** " 操作。

   ![选择“响应”操作](./media/connectors-native-reqres/select-response-action.png)

   为简明起见，本示例中“请求”触发器已折叠。

1. 添加响应消息所需的任何值。

   单击某些字段中的框会打开动态内容列表。 然后，可以从工作流中的前面步骤选择表示可用输出的标记。 在上述示例中指定的架构内的属性现在会显示在动态内容列表中。

   例如，对于“标头”框，请包含 `Content-Type` 作为键名称，并将键值设置为 `application/json`，如本主题前面所述。 对于“正文”框，可以从动态内容列表中选择触发器正文输出。

   ![“响应”操作详细信息](./media/connectors-native-reqres/response-details.png)

   若要查看 JSON 格式的标头，请选择“切换到文本视图”。

   ![标头 - 切换到文本视图](./media/connectors-native-reqres/switch-to-text-view.png)

   下面是有关可在“响应”操作中设置的属性的详细信息。

   | 属性名称 | JSON 属性名称 | 必须 | 说明 |
   |---------------|--------------------|----------|-------------|
   | **状态代码** | `statusCode` | 是 | 要在响应中返回的状态代码 |
   | **标头** | `headers` | 否 | 一个 JSON 对象，描述要包含在响应中的一个或多个标头 |
   | **正文** | `body` | 否 | 响应正文 |
   |||||

1. 若要添加其他属性，例如响应正文的 JSON 架构，请打开“添加新参数”列表，并选择要添加的参数。

1. 完成后，保存逻辑应用。 在设计器工具栏上选择“保存”。

## <a name="next-steps"></a>后续步骤

* [对基于请求的触发器的入站调用进行安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [适用于逻辑应用的连接器](../connectors/apis-list.md)