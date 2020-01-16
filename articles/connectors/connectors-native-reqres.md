---
title: 接收和响应 HTTPS 调用
description: 使用 Azure 逻辑应用实时处理 HTTPS 请求和事件
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 10/11/2019
tags: connectors
ms.openlocfilehash: 822a6d1cd812ead8e677a66a9b1e47ebdbcf8aea
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030141"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用接收和响应传入的 HTTPS 调用

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置请求触发器或响应操作，你可以创建可接收和响应传入 HTTPS 请求的自动化任务和工作流。 例如，你可以创建逻辑应用：

* 接收和响应对本地数据库中的数据的 HTTPS 请求。
* 发生外部 webhook 事件时触发工作流。
* 接收来自另一个逻辑应用的 HTTPS 调用并对其作出响应。

> [!NOTE]
> 对于传入呼叫，请求触发器*仅*支持传输层安全性（TLS）1.2。 传出呼叫继续支持 TLS 1.0、1.1 和1.2。 如果出现 SSL 握手错误，请确保使用 TLS 1.2。 对于传入调用，以下是受支持的密码套件：
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请学习[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-request"></a>

## <a name="add-request-trigger"></a>添加请求触发器

此内置触发器创建可*仅*接收传入 https 请求的手动可调用 https 终结点。 发生此事件时，触发器触发并运行逻辑应用。 有关触发器的基础 JSON 定义以及如何调用此触发器的详细信息，请参阅[请求触发器类型](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)和[在 Azure 逻辑应用中通过 HTTP 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器打开后，在 "搜索" 框中输入 "http 请求" 作为筛选器。 从 "触发器" 列表中，选择 "**收到 HTTP 请求时**" 触发器，这是逻辑应用工作流中的第一步。

   ![选择请求触发器](./media/connectors-native-reqres/select-request-trigger.png)

   Request 触发器显示了以下属性：

   ![请求触发器](./media/connectors-native-reqres/request-trigger.png)

   | 属性名称 | JSON 属性名称 | 需要 | Description |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {无} | 是 | 保存逻辑应用并用于调用逻辑应用后生成的终结点 URL |
   | **请求正文 JSON 架构** | `schema` | 否 | 描述传入请求正文中的属性和值的 JSON 架构 |
   |||||

1. 在 "**请求正文 JSON 架构**" 框中，选择性地输入描述传入请求中的正文的 JSON 架构，例如：

   ![示例 JSON 架构](./media/connectors-native-reqres/provide-json-schema.png)

   设计器使用此架构为请求中的属性生成标记。 这样一来，逻辑应用就可以分析、使用数据，并通过触发器将数据传递到工作流。

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

   输入 JSON 架构时，设计器会显示一个提醒，其中包含请求中的 `Content-Type` 标头，并将该标头值设置为 `application/json`。 有关详细信息，请参阅[处理内容类型](../logic-apps/logic-apps-content-type.md)。

   ![提醒包括 "Content-type" 标头](./media/connectors-native-reqres/include-content-type.png)

   下面是此标头的 JSON 格式：

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   若要生成基于预期负载（数据）的 JSON 架构，可以使用工具（如[JSONSchema.net](https://jsonschema.net)），也可以执行以下步骤：

   1. 在请求触发器中，选择“使用示例有效负载生成架构”。

      ![从负载生成架构](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 输入示例有效负载，然后选择 "**完成**"。

      ![从负载生成架构](./media/connectors-native-reqres/enter-payload.png)

      下面是示例负载：

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

1. 若要指定其他属性，请打开 "**添加新参数**" 列表，然后选择要添加的参数。

   | 属性名称 | JSON 属性名称 | 需要 | Description |
   |---------------|--------------------|----------|-------------|
   | **方法** | `method` | 否 | 传入请求必须用于调用逻辑应用的方法 |
   | **相对路径** | `relativePath` | 否 | 逻辑应用的终结点 URL 可以接受的参数的相对路径 |
   |||||

   此示例将添加**方法**属性：

   ![添加方法参数](./media/connectors-native-reqres/add-parameters.png)

   **方法**属性将出现在触发器中，以便您可以从列表中选择一个方法。

   ![Select 方法](./media/connectors-native-reqres/select-method.png)

1. 现在，添加另一个操作作为工作流中的下一步。 在触发器下，选择 "**下一步**"，以便可以找到要添加的操作。

   例如，你可以通过[添加响应操作](#add-response)来响应请求，你可以使用该操作返回自定义响应，并在本主题后面部分介绍。

   逻辑应用使传入请求仅打开一分钟。 假定逻辑应用工作流包含响应操作，如果逻辑应用在这段时间后未返回响应，则逻辑应用会向调用方返回 `504 GATEWAY TIMEOUT`。 否则，如果逻辑应用不包括响应操作，则逻辑应用会立即向调用方返回 `202 ACCEPTED` 响应。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”。 

   此步骤将生成用于发送触发逻辑应用的请求的 URL。 若要复制此 URL，请选择 URL 旁边的 "复制" 图标。

   ![用于触发逻辑应用的 URL](./media/connectors-native-reqres/generated-url.png)

1. 若要触发逻辑应用，请将 HTTP POST 发送到生成的 URL。 例如，可以使用[Postman](https://www.getpostman.com/)之类的工具。

### <a name="trigger-outputs"></a>触发器输出

下面详细介绍了 Request 触发器的输出：

| JSON 属性名称 | 数据类型 | Description |
|--------------------|-----------|-------------|
| `headers` | 对象 | 描述请求中的标头的 JSON 对象 |
| `body` | 对象 | 一个 JSON 对象，用于描述请求中的正文内容 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>添加响应操作

你可以使用响应操作来使用负载（数据）来响应传入 HTTPS 请求，但仅在由 HTTPS 请求触发的逻辑应用中进行响应。 你可以在工作流中的任何时间点添加 "响应" 操作。 有关此触发器的基础 JSON 定义的详细信息，请参阅[响应操作类型](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)。

逻辑应用使传入请求仅打开一分钟。 假定逻辑应用工作流包含响应操作，如果逻辑应用在这段时间后未返回响应，则逻辑应用会向调用方返回 `504 GATEWAY TIMEOUT`。 否则，如果逻辑应用不包括响应操作，则逻辑应用会立即向调用方返回 `202 ACCEPTED` 响应。

1. 在逻辑应用设计器中，在要添加响应操作的步骤下，选择 "**新建步骤**"。

   例如，使用前面的 Request 触发器：

   ![添加新步骤](./media/connectors-native-reqres/add-response.png)

   若要在步骤之间添加操作，请将指针移到这些步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框中，输入 "响应" 作为筛选器，然后选择 "**响应**" 操作。

   ![选择响应操作](./media/connectors-native-reqres/select-response-action.png)

   为了简单起见，此示例中折叠了 Request 触发器。

1. 添加响应消息所需的任何值。 

   在某些字段中，单击其框内会打开动态内容列表。 然后，可以从工作流中的前面步骤中选择表示可用输出的标记。 在上述示例中指定的架构中的属性现在显示在 "动态内容" 列表中。

   例如，对于 "**标头**" 框，将 `Content-Type` 包含为密钥名称，并将 "密钥" 值设置为 `application/json` 如本主题前面所述。 对于 "**正文**" 框，可以从动态内容列表中选择触发器正文输出。

   ![响应操作详细信息](./media/connectors-native-reqres/response-details.png)

   若要查看 JSON 格式的标头，请选择 "**切换到文本视图**"。

   ![标头-切换到文本视图](./media/connectors-native-reqres/switch-to-text-view.png)

   下面是有关可以在响应操作中设置的属性的详细信息。 

   | 属性名称 | JSON 属性名称 | 需要 | Description |
   |---------------|--------------------|----------|-------------|
   | **状态代码** | `statusCode` | 是 | 要在响应中返回的状态代码 |
   | **标头** | `headers` | 否 | 一个 JSON 对象，描述要包括在响应中的一个或多个标头 |
   | **正文** | `body` | 否 | 响应正文 |
   |||||

1. 若要指定其他属性（例如响应正文的 JSON 架构），请打开 "**添加新参数**" 列表，然后选择要添加的参数。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”。 

## <a name="next-steps"></a>后续步骤

* [逻辑应用的连接器](../connectors/apis-list.md)
