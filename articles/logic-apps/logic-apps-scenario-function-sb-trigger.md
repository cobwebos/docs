---
title: 方案 - 使用 Azure Functions 和 Azure 服务总线触发逻辑应用 | Microsoft Docs
description: 使用 Azure Functions 和 Azure 服务总线创建触发逻辑应用的函数
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 1d3c4039ae823d3797e768af5892333d4d925268
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57789935"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>场景：使用 Azure Functions 和 Azure 服务总线触发逻辑应用

需要部署长时间运行的侦听器或任务时，可以使用 Azure Functions 为逻辑应用创建触发器。 例如，可以创建一个函数以侦听队列，并立即以推送触发器的形式触发逻辑应用。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识 

* 若要创建 Azure 函数，首先需要[创建函数应用](../azure-functions/functions-create-function-app-portal.md)。

## <a name="create-logic-app"></a>创建逻辑应用

在此示例中，需要触发的每个逻辑应用都有一个运行的函数。 首先，创建一个具有 HTTP 请求触发器的逻辑应用。 每当收到队列消息时，函数会调用该终结点。  

1. 登录到 [Azure 门户](https://portal.azure.com)，并创建一个空的逻辑应用。 

   如果不熟悉逻辑应用，查看[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在搜索框中，输入“http 请求”。 在触发器列表中选择此触发器：**当收到 HTTP 请求时**

   ![选择触发器](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. 对于**请求**触发器，还可以输入要用于队列消息的 JSON 架构。 JSON 架构帮助逻辑应用设计器理解输入数据的结构并使你在整个工作流中可以更容易地选择输出。 

   若要指定架构，请在“请求正文 JSON 架构”框中输入架构，例如： 

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

1. 添加你希望在收到队列消息后发生的任何其他操作。 

   例如，可以通过 Office 365 Outlook 连接器发送一封电子邮件。

1. 保存你的逻辑应用，这将在此逻辑应用中生成触发器的回调 URL。 此 URL 显示在 **HTTP POST URL** 属性中。

   ![为触发器生成的回调 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>创建 Azure 函数

接下来，创建一个充当触发器并侦听队列的函数。 

1. 在 Azure 门户中，打开并展开你的函数应用（如果尚未打开）。 

1. 在你的函数应用名称下，展开“函数”。 在“函数”窗格中，选择“新建函数”。 选择此模板：**服务总线队列触发器-C#**
   
   ![选择 Azure Functions 门户](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. 为你的触发器提供一个名称，然后配置与服务总线队列之间的连接，该连接使用 Azure 服务总线 SDK `OnMessageReceive()` 侦听器。

1. 编写一个基本函数，用以通过将队列消息用作触发器来调用之前创建的逻辑应用终结点，例如： 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   此示例使用 `application/json` 消息内容类型，不过可以根据需要更改此类型。

1. 若要测试函数，请使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)之类的工具添加一个队列消息。 

   逻辑应用在该函数收到消息之后立即触发。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

