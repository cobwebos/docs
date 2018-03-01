---
title: "Azure 服务总线到事件网格集成示例 | Microsoft Docs"
description: "服务总线消息传送和事件网格集成的示例"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 2a4d17673340d145de9a3514f920c74f7eebf6b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Azure 服务总线到 Azure 事件网格示例

本文档介绍如何设置 Azure Functions 和逻辑应用，以便根据从事件网格接收事件的情况来接收消息。 示例假定服务总线主题有两个订阅，但在创建事件网格订阅时，采用只为一个服务总线订阅发送事件的方式。 然后，你向服务总线主题发送消息，并验证是否已为该服务总线订阅生成事件，然后函数或逻辑应用就会从该服务总线订阅接收消息并将其完成。

* 首先请确保已将所有[先决条件](#prerequisites)准备到位。
* 创建一个[简单的测试性 Azure Function](#test-function-setup)，以便调试并查看从事件网格发出的初始事件流。  **不管是否执行步骤 3 或 4，都应执行此步骤。**
* 创建一个根据事件网格事件[接收和处理服务总线消息的 Azure Function](#receive-messages-using-azure-function)。
* 利用[逻辑应用](#receive-messages-using-azure-logic-app)。

## <a name="prerequisites"></a>先决条件

### <a name="service-bus-namespace"></a>服务总线命名空间

创建服务总线高级命名空间。 创建包含两个订阅的服务总线主题。

### <a name="code-to-send-message-to-the-service-bus-topic"></a>用于将消息发送到服务总线主题的代码

可以使用任何方法向服务总线主题发送消息。 也可使用下面提供的示例。 此示例代码假定你使用 Visual Studio 2017。

克隆[此 GitHub 存储库](https://github.com/Azure/azure-service-bus/)。

导航到以下文件夹：

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration，打开 SBEventGridIntegration.sln 文件。

然后，导航到项目 MessageSender 并打开 Program.cs。

![8][]

填充主题名称和连接字符串，然后执行控制台应用程序：

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>测试性函数设置

在完成整个端到端方案之前，需要使用至少一个小型测试函数来调试和查看流动的具体事件。

在门户中创建新的 Azure Function 应用程序。 访问此[链接](https://docs.microsoft.com/en-us/azure/azure-functions/)，了解 Azure Functions 的基础知识。

在新创建的函数中，单击这个小加号即可添加 http 触发器函数：

![2][]

![3][]

然后，将以下代码复制到函数中：

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

单击“保存并运行”。

## <a name="connect-function-and-namespace-via-event-grid"></a>通过事件网格连接函数和命名空间

下一步是将函数和服务总线命名空间绑定在一起。 就此示例来说，请使用 Azure 门户。 请参阅[概念](service-bus-to-event-grid-integration-concept.md 页，了解如何使用 PowerShell 或 Azure CLI 来达到同一效果。

若要创建新的 Azure 事件网格订阅，请导航到 Azure 门户中的命名空间，然后选择“事件网格”边栏选项卡。 单击“+ 事件订阅”。

以下屏幕截图显示了一个命名空间，其中已经有一些事件网格订阅。

![20][]

以下屏幕截图显示了如何在没有特定筛选的情况下订阅 Azure Function 或 Web 挂钩。 记住将服务总线订阅的相关筛选器作为“后缀筛选器”添加：

![21][]

如先决条件中所述，向服务总线主题发送一条消息，然后通过 Azure Function 的“监视”功能验证事件是否正在流动。

![9][]

### <a name="receive-messages-using-azure-function"></a>使用 Azure Function 接收消息

上一部分介绍了一个简单的测试和调试方案，确保了事件在流动。 本文档的此部分将介绍如何在收到事件后接收和处理消息。

之所以按以下方式添加 Azure Function，是因为 Azure Functions 本身中的服务总线函数本来是不支持新的事件网格集成的。

在先决条件中已打开的同一 Visual Studio 解决方案中，选择 ReceiveMessagesOnEvent.cs。 在代码中输入连接字符串：

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

然后转到 Azure 门户，下载此前在 [2.测试性函数设置](#2-test-function-setup)中创建的 Azure Function 的发布配置文件。

![11][]

然后在 Visual Studio 中右键单击“SBEventGridIntegration”并选择“发布”。 使用以前下载的发布配置文件，选择导入配置文件，然后单击“发布”。

![12][]

发布新的 Azure Function 以后，请创建新的 Azure 事件网格订阅，使之指向新的 Azure Function。 确保应用正确的“结尾为”筛选器，该筛选器应该是服务总线订阅名称。

然后，向以前创建的 Azure 服务总线主题发送一条消息，并在门户的 Azure Function 日志中进行检查，确保事件正在流动且消息已收到。

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>使用 Azure 逻辑应用接收消息

以下说明介绍如何将 Azure 逻辑应用与 Azure 服务总线及 Azure 事件网格连接到一起：

首先，在 Azure 门户中创建新的逻辑应用，然后选择事件网格作为启动操作。

![13][]

逻辑应用设计器中的初始视图应如以下屏幕截图所示，只是你需要将“资源名称”替换为自己的命名空间名称。 另请确保展开高级选项，为订阅添加后缀筛选器：

![14][]

然后，添加服务总线“接收”操作，以便从主题订阅接收内容。 最终操作应如以下屏幕截图所示。

![15][]

然后添加完成事件，应如下所示。

![16][]

保存逻辑应用并向服务总线主题发送消息，如先决条件中所述。 然后观察逻辑应用的执行情况。 如果单击“概览”，然后单击“运行历史记录”，也可获得有关执行情况的更多数据。

![17][]

![18][]

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/)。
* 详细了解 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。
* 了解有关 [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)的详细信息。
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
