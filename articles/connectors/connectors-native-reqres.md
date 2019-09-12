---
title: 响应 HTTP 或 HTTPS 请求-Azure 逻辑应用
description: 使用 Azure 逻辑应用对 HTTP 或 HTTPS 实时响应事件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewers: klam, LADocs
manager: carmonm
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.topic: article
ms.date: 09/06/2019
tags: connectors
ms.openlocfilehash: c707326c810f111db220be1ffdfbcd4a52dbb2d6
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898292"
---
# <a name="respond-to-http-or-https-requests-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用响应 HTTP 或 HTTPS 请求

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置请求触发器或响应操作，你可以创建自动任务和工作流，以对通过 HTTP 或 HTTPS 发送的事件做出反应并进行实时响应。 例如，你可以创建逻辑应用：

* 响应对本地数据库中的数据的 HTTP 请求。
* 发生外部 webhook 事件时触发工作流。
* 从另一个逻辑应用中调用逻辑应用。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[逻辑应用](../logic-apps/logic-apps-overview.md)的基本知识。 如果不熟悉逻辑应用，请了解[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="add-request"></a>

## <a name="add-the-request-trigger"></a>添加“请求”触发器

此内置触发器创建一个终结点，该终结点等待并通过 HTTP 或 HTTPS 接收传入的请求。 发生此事件时，触发器触发并运行逻辑应用。

1. 登录到 [Azure 门户](https://portal.azure.com)。 创建空白逻辑应用。

1. 逻辑应用设计器打开后，在 "搜索" 框中输入 "http 请求" 作为筛选器。 从 "触发器" 列表中，选择 "**收到 HTTP 请求时**" 触发器，这是逻辑应用工作流中的第一步。

   ![选择 HTTP 请求触发器](./media/connectors-native-reqres/select-request-trigger.png)

1. 对于 "**请求正文 JSON 架构**" 框，可以根据需要输入 JSON 架构，该架构描述传入请求中预期的 HTTP 请求正文，例如：

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

   输入 JSON 架构时，设计器将显示一个提醒，其中包含请求`Content-Type`中的标头，并将该标头`application/json`值设置为。 有关详细信息，请参阅[处理内容类型](../logic-apps/logic-apps-content-type.md)。

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

1. 若要指定其他属性（例如用于请求的预期方法或相对路径），请打开 "**添加新参数**" 列表，然后选择要添加的参数。

   此示例将添加**方法**参数：

   ![添加方法参数](./media/connectors-native-reqres/add-parameters.png)

   现在**方法**参数出现在触发器中，以便您可以从列表中选择一个方法。

   ![选择方法](./media/connectors-native-reqres/select-method.png)

1. 现在，添加另一个操作作为工作流中的下一步。 在触发器下，选择 "**下一步**"，以便可以找到要添加的操作。

   例如，你可以通过[添加响应操作](#add-response)来响应请求，本主题稍后将对此进行介绍。

   > [!NOTE]
   > 如果不在逻辑应用工作流中包括并定义响应操作，则逻辑应用会立即将`202 ACCEPTED`响应返回给调用方。 您可以使用响应操作来自定义响应。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”。 

   此步骤将生成用于发送触发逻辑应用的请求的 URL。 若要复制此 URL，请选择 URL 旁边的 "复制" 图标。

   ![用于触发逻辑应用的 URL](./media/connectors-native-reqres/generated-url.png)

1. 若要触发逻辑应用，请将 HTTP POST 发送到生成的 URL。 例如，可以使用[Postman](https://www.getpostman.com/)之类的工具。

### <a name="trigger-outputs"></a>触发器输出

下面详细介绍了 Request 触发器的输出：

| 属性名 | 数据类型 | 描述 |
|---------------|-----------|-------------|
| `headers` | Object | 描述请求中的标头的 JSON 对象 |
| `body` | Object | 一个 JSON 对象，用于描述请求中的正文内容 |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>添加响应操作

你可以使用响应操作来自定义响应，但仅在由 HTTP 请求触发的逻辑应用工作流中进行自定义。 你可以在工作流中的任何时间点添加 "响应" 操作。

逻辑应用使传入请求仅打开一分钟。 假定逻辑应用工作流包含响应操作，如果逻辑应用在这段时间后未返回响应，则逻辑应用会将返回`504 GATEWAY TIMEOUT`到调用方。 但是，如果逻辑应用不包括响应操作，则逻辑应用会立即将`202 ACCEPTED`响应返回给调用方。 

1. 在逻辑应用设计器中，在要添加响应操作的步骤下，选择 "**新建步骤**"。

   例如，使用前面的 Request 触发器：

   ![添加新步骤](./media/connectors-native-reqres/add-response.png)

   若要在步骤之间添加操作，请将指针移到这些步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在 "**选择操作**" 下的 "搜索" 框中，输入 "响应" 作为筛选器，然后选择 "**响应**" 操作。

   ![选择响应操作](./media/connectors-native-reqres/select-response-action.png)

   为了简单起见，此示例中折叠了 Request 触发器。

1. 添加响应消息所需的任何值。 

   在某些字段中，单击其框内会打开动态内容列表。 然后，可以从工作流中的前面步骤中选择表示可用输出的标记。 在上述示例中指定的架构中的属性现在显示在 "动态内容" 列表中。

   例如，在 "**标头**" 字段中`Content-Type` ，将作为键名称，将键值设置为`application/json`，如本主题前面所述。 对于 "**正文**" 字段，您可以从动态内容列表中选择触发器正文。

   ![响应操作详细信息](./media/connectors-native-reqres/response-details.png)

   若要查看 JSON 格式的标头，请选择 "**切换到文本视图**"。

   ![标头-切换到文本视图](./media/connectors-native-reqres/switch-to-text-view.png)

   下面是有关可以在响应操作中设置的属性的详细信息。 

   | Display name | 属性名 | 必填 | 描述 |
   |--------------|---------------|----------|-------------|
   | 状态代码 | `statusCode` | 是 | 要返回的 HTTP 状态代码 |
   | 标头 | `headers` | 否 | 一个 JSON 对象，描述要包括的任何响应标头。  |
   | Body | `body` | 否 | 响应正文 |
   |||||

1. 若要指定其他属性（例如响应的 JSON 架构），请打开 "**添加新参数**" 列表，然后选择要添加的参数。

1. 完成后，保存逻辑应用。 在设计器工具栏上，选择“保存”。 

## <a name="next-steps"></a>后续步骤

* [适用于逻辑应用的连接器](../connectors/apis-list.md)