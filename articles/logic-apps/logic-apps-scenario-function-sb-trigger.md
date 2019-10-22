---
title: 通过 Azure Functions 调用逻辑应用-Azure 逻辑应用
description: 通过侦听 Azure 服务总线，创建调用或触发逻辑应用的 Azure 函数
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 2ab6ace7c30c3dd385928b6b0ae8000485d5f495
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680144"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>使用 Azure Functions 和 Azure 服务总线调用或触发逻辑应用

当需要部署长时间运行的侦听器或任务时，可以使用[Azure Functions](../azure-functions/functions-overview.md)来触发逻辑应用。 例如，可以创建一个在[Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)队列上侦听的 azure 函数，并立即将逻辑应用作为推送触发器。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* Azure 服务总线命名空间。 如果没有命名空间，请[先创建命名空间](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* Azure function app，它是 Azure 函数的容器。 如果没有函数应用，请[先创建 function app](../azure-functions/functions-create-first-azure-function.md)，并确保选择 .net 作为运行时堆栈。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

## <a name="create-logic-app"></a>创建逻辑应用

对于这种情况，您有一个运行要触发的每个逻辑应用的函数。 首先，创建一个以 HTTP 请求触发器开头的逻辑应用。 每当收到队列消息时，函数会调用该终结点。  

1. 登录到 [Azure 门户](https://portal.azure.com)，并创建一个空的逻辑应用。

   如果你不熟悉逻辑应用，请查看[快速入门：创建你的第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在搜索框中，输入“http 请求”。 从触发器列表中选择此触发器：“当收到 HTTP 请求时”

   ![选择触发器](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   使用请求触发器时，可以选择输入要与队列消息一起使用的 JSON 架构。 JSON 架构有助于逻辑应用设计器了解输入数据的结构，并使输出更易于在工作流中使用。

1. 若要指定架构，请在“请求正文 JSON 架构”框中输入架构，例如：

   ![指定 JSON 架构](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   如果你没有架构，但有一个 JSON 格式的示例有效负载，则可以基于该有效负载生成一个架构。

   1. 在请求触发器中，选择“使用示例有效负载生成架构”。

   1. 在“输入或粘贴示例 JSON 有效负载”下，输入你的示例有效负载，然后选择“完成”。

      ![输入示例有效负载](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   此示例有效负载生成在触发器中显示的以下架构：

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. 添加要在接收队列消息之后运行的任何其他操作。

   例如，可以通过 Office 365 Outlook 连接器发送一封电子邮件。

1. 保存你的逻辑应用，这将在此逻辑应用中生成触发器的回调 URL。 稍后，在 Azure 服务总线队列触发器的代码中使用此回调 URL。

   回调 URL 出现在 " **HTTP POST url** " 属性中。

   ![为触发器生成的回调 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>创建 Azure 函数

接下来，创建一个充当触发器并侦听队列的函数。

1. 在 Azure 门户中，打开并展开你的函数应用（如果尚未打开）。 

1. 在你的函数应用名称下，展开“函数”。 在“函数”窗格中，选择“新建函数”。

   ![展开 "函数"，然后选择 "新建函数"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. 根据你创建的新函数应用，选择 ".NET" 作为 "运行时堆栈"，或使用现有函数应用来选择此模板。

   * 对于新的函数应用，选择此模板：**服务总线队列触发器**

     ![为新的 function app 选择模板](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 对于现有的函数应用，选择此模板：**服务总线队列触发器- C#**

     ![为现有 function app 选择模板](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. 在 " **Azure 服务总线队列" 触发器**窗格上，为触发器提供一个名称，并设置队列的**服务总线连接**，该连接使用 Azure 服务总线 SDK `OnMessageReceive()` 侦听器，然后选择 "**创建**"。

1. 编写一个基本函数，通过将队列消息用作触发器来调用以前创建的逻辑应用终结点。 此示例使用 `application/json` 消息内容类型，不过可以根据需要更改此类型。 如果可能，请重复使用 HTTP 客户端的实例。 有关详细信息，请参阅[管理 Azure Functions 中的连接](../azure-functions/manage-connections.md)。

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. 若要测试函数，请使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)之类的工具添加一个队列消息。

   逻辑应用在该函数收到消息之后立即触发。

## <a name="next-steps"></a>后续步骤

[使用 HTTP 终结点调用、触发或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)