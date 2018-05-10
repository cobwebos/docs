---
title: Azure 服务总线到事件网格集成示例 | Microsoft Docs
description: 本文提供的示例涉及服务总线消息传送和事件网格集成。
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 5d0ab8cf9e87fc13b78b00dbe77ec6f9fb38c4b9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Azure 服务总线到 Azure 事件网格集成示例

本文介绍如何设置 Azure 函数和逻辑应用，以便根据从 Azure 事件网格接收事件的情况来接收消息。 可以执行以下操作：
 
* 创建一个[简单的测试性 Azure 函数](#test-function-setup)，以便调试并查看从事件网格发出的初始事件流。 执行此步骤，不管是否执行其他步骤。
* 创建一个根据事件网格事件[接收和处理 Azure 服务总线消息的 Azure 函数](#receive-messages-using-azure-function)。
* 利用 [Azure 应用服务的逻辑应用功能](#receive-messages-using-azure-logic-app)。

创建的示例假定服务总线主题有两个订阅。 示例还假定，创建事件网格订阅的目的是只为一个服务总线订阅发送事件。 

在示例中，你将消息发送到服务总线主题，然后验证是否已为此服务总线订阅生成事件。 函数或逻辑应用先从服务总线订阅接收消息，然后将其完成。

## <a name="prerequisites"></a>先决条件
开始之前，请确保已完成后续两个部分中的步骤。

### <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

创建一个服务总线高级命名空间，然后创建一个服务总线主题，其中包含两个订阅。

### <a name="send-a-message-to-the-service-bus-topic"></a>向服务总线主题发送消息

可以使用任何方法向服务总线主题发送消息。 此过程末尾的示例代码假定你使用 Visual Studio 2017。

1. 克隆 [GitHub azure-service-bus 存储库](https://github.com/Azure/azure-service-bus/)。

2. 在 Visual Studio 中转到 *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* 文件夹，然后打开 *SBEventGridIntegration.sln* 文件。

3. 转到 **MessageSender** 项目，然后选择 **Program.cs**。

   ![8][]

4. 填充主题名称和连接字符串，然后执行以下控制台应用程序代码：

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>设置测试函数

在完成整个方案之前，请设置至少一个小型测试函数，以便调试和观察流动的具体事件。

1. 在 Azure 门户中创建新的 Azure Functions 应用程序。 若要了解 Azure Functions 的基础知识，请参阅 [Azure Functions 文档](https://docs.microsoft.com/azure/azure-functions/)。

2. 在新创建的函数中，选择加号 (+) 即可添加 HTTP 触发器函数：

    ![2][]
    
    此时会打开“预制函数快速入门”窗口。

    ![3][]

3. 依次选择“Webhook + API”按钮、“CSharp”、“创建此函数”。
 
4. 将以下代码粘贴到函数中：

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

5. 选择“保存并运行”。

## <a name="connect-the-function-and-namespace-via-event-grid"></a>通过事件网格连接函数和命名空间

此部分将函数和服务总线命名空间绑定在一起。 就此示例来说，请使用 Azure 门户。 若要了解如何使用 PowerShell 或 Azure CLI 来完成此过程，请参阅 [Azure 服务总线到 Azure 事件网格集成概述](service-bus-to-event-grid-integration-concept.md)。

若要创建 Azure 事件网格订阅，请执行以下操作：
1. 在 Azure 门户中转到自己的命名空间，然后在左窗格中选择“事件网格”。  
    此时会打开命名空间窗口，两个事件网格订阅显示在右窗格中。

    ![20][]

2. 选择“事件订阅”。  
    此时会打开“事件订阅”窗口。 下图显示了一个窗体，用于订阅 Azure 函数或 Webhook 而不需应用筛选器。

    ![21][]

3. 完成如图所示的窗体，然后记住在“后缀筛选器”框中输入相关筛选器。

4. 选择**创建**。

5. 如“先决条件”部分所述，向服务总线主题发送一条消息，然后通过 Azure Functions 的“监视”功能验证事件是否正在流动。

下一步是将函数和服务总线命名空间绑定在一起。 就此示例来说，请使用 Azure 门户。 若要了解如何使用 PowerShell 或 Azure CLI 来执行此步骤，请参阅 [Azure 服务总线到 Azure 事件网格集成概述](service-bus-to-event-grid-integration-concept.md)。

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>使用 Azure Functions 接收消息

上一部分观察了一个简单的测试和调试方案，确保了事件在流动。 

此部分将介绍如何在收到事件后接收和处理消息。

之所以按以下示例中的方式添加 Azure 函数，是因为 Azure Functions 中的服务总线函数本来就不支持新的事件网格集成。

1. 在先决条件中已打开的同一 Visual Studio 解决方案中，选择 **ReceiveMessagesOnEvent.cs**。 

    ![10][]

2. 在以下代码中输入连接字符串：

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. 在 Azure 门户中下载 Azure 函数的发布配置文件，该函数是在“设置测试函数”部分创建的。

    ![11][]

4. 在 Visual Studio 中右键单击“SBEventGridIntegration”，然后选择“发布”。 

5. 在以前下载的发布配置文件所对应的“发布”窗格中，选择“导入配置文件”，然后选择“发布”。

    ![12][]

6. 发布新的 Azure 函数以后，请创建新的 Azure 事件网格订阅，使之指向新的 Azure 函数。  
    在“结尾为”框中，确保应用正确的筛选器，该筛选器应该是服务总线订阅名称。

7. 向以前创建的 Azure 服务总线主题发送一条消息，然后在 Azure 门户中监视 Azure Functions 日志，确保事件正在流动且消息正在进行接收。

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>使用逻辑应用接收消息

通过执行以下操作，将逻辑应用与 Azure 服务总线及 Azure 事件网格连接到一起：

1. 在 Azure 门户中创建新的逻辑应用，然后选择“事件网格”作为启动操作。

    ![13][]

    此时会打开“逻辑应用设计器”窗口。

    ![14][]

2. 若要添加信息，请执行以下操作：

    a. 在“资源名称”框中，输入自己的命名空间名称。 

    b. 在“高级选项”下的“后缀筛选器”框中，输入订阅的筛选器。

3. 添加服务总线“接收”操作，以便从主题订阅接收消息。  
    下图显示了最终操作：

    ![15][]

4. 添加一个完整事件，如下图所示：

    ![16][]

5. 保存逻辑应用并向服务总线主题发送消息，如“先决条件”部分所述。  
    观察逻辑应用的执行情况。 若要查看执行的更多数据，请选择“概览”，然后查看“运行历史记录”下的数据。

    ![17][]

    ![18][]

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/)。
* 详细了解 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。
* 详细了解 [Azure 应用服务的逻辑应用功能](https://docs.microsoft.com/azure/logic-apps/)。
* 详细了解 [Azure 服务总线](https://docs.microsoft.com/azure/service-bus/)。


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
