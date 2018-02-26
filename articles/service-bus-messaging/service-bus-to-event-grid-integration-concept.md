---
title: "Azure 服务总线到事件网格集成概述 | Microsoft Docs"
description: "服务总线消息传送和事件网格集成的说明"
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
ms.openlocfilehash: 17f6c107ea1adfd4463ff4cc205fe11d111acb84
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Azure 服务总线到 Azure 事件网格集成概述

Azure 服务总线已启动到 Azure 事件网格的新集成。 此功能所启用的主要方案是，消息量不大的服务总线队列或订阅不需要始终有一个接收器来轮询消息。 现在，当队列或订阅中存在消息时，服务总线可以在没有接收器存在的情况下，将事件发出到 Azure 事件网格。 可以创建针对服务总线命名空间的 Azure 事件网格订阅，并通过启动接收器来侦听和响应这些事件。 有了此功能，就可以将服务总线用于响应式编程模型。

若要启用此功能，需要具备以下条件：

* 一个包含至少一个服务总线队列的 Azure 服务总线高级命名空间，或者一个包含至少一个订阅的服务总线主题。
* 对 Azure 服务总线命名空间的“参与者”访问权限。
* 此外，还需要服务总线命名空间的 Azure 事件网格订阅。 存在可收集的消息时，此订阅是从 Azure 事件网格获得通知。 通常的订户可能包括逻辑应用、Azure Functions 或 Web 挂钩，后者会联系 Web 应用来处理消息。 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>验证你是否有参与者访问权限

导航到服务总线命名空间，然后选择“访问控制(标识和访问管理)”，如下所示：

![1][]

### <a name="events-and-event-schemas"></a>事件和事件架构

目前，Azure 服务总线会针对两种情况发送事件。

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

另外，它会使用标准的 Azure 事件网格安全性和[身份验证机制](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication)。

若要获取有关事件网格事件架构的更多详细信息，请访问[此](https://docs.microsoft.com/en-us/azure/event-grid/event-schema)链接。

#### <a name="active-messages-available-event"></a>活动消息可用事件

如果队列或订阅中存在活动的消息，但却没有接收器侦听，则会生成此事件。

此事件的架构如下：

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>死信消息可用事件

对于每个有消息但没有活动接收器的死信队列，你会获得至少一个事件。

此事件的架构如下：

```JSON
[{
     "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>发出事件的频率和数量如何？

如果命名空间中有多个队列和主题/订阅，则每个队列你会获得至少一个事件，每个订阅也一样。 如果服务总线实体中没有消息，事件会立即发出，然后新消息就会到达，或者会每两分钟发出一次，除非 Azure 服务总线检测到活动接收器。 对消息进行浏览不会导致事件中断。

默认情况下，Azure 服务总线会针对命名空间中的所有实体发出事件。 如果只需获取特定实体的事件，请参阅下面的筛选部分。

### <a name="filtering-limiting-from-where-you-get-events"></a>筛选：限制获取事件的位置

如果只需获取特定位置的事件（例如，命名空间中某个队列或订阅的事件），则可使用 Azure 事件网格提供的“开头为”或“结尾为”筛选器。 在某些界面中，这两个筛选器称为“前缀”和“后缀”筛选器。 如果需要获取多个（但并非所有）队列和订阅的事件，则可创建多个不同的 Azure 事件网格订阅，并为每个订阅提供一个筛选器。

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>如何为服务总线命名空间创建 Azure 事件网格订阅

可以使用三种不同方法，为服务总线命名空间创建事件网格订阅。

* [Azure 门户](#portal-instructions)
* [Azure CLI](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>门户说明

若要创建新的 Azure 事件网格订阅，请导航到 Azure 门户中的命名空间，然后选择“事件网格”边栏选项卡。 单击下面的“+ 事件订阅”即可显示一个命名空间，其中已经有一些事件网格订阅。

![20][]

以下屏幕截图显示了一个示例，介绍了如何在没有特定筛选的情况下订阅 Azure Function 或 Web 挂钩：

![21][]

## <a name="azure-cli-instructions"></a>Azure CLI 说明

首先，请确保至少已安装 Azure CLI 2.0 版。 可以在此处下载安装程序。 然后按“Windows + X”，以管理员权限打开新的 PowerShell 控制台。 也可在 Azure 门户中使用命令外壳。

执行以下代码：

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell 说明

确保已安装 Azure PowerShell。 可在此处找到它。 然后按“Windows + X”，以管理员权限打开新的 PowerShell 控制台。 也可在 Azure 门户中使用命令外壳。

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

可以在这里浏览其他设置选项，或者[测试事件是否正在流动](#test-that-events-are-flowing)。

## <a name="next-steps"></a>后续步骤

* 服务总线和事件网格[示例](service-bus-to-event-grid-integration-example.md)。
* 详细了解 [Azure 事件网格](https://docs.microsoft.com/en-us/azure/azure-functions/)。
* 详细了解 [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/)。
* 了解有关 [Azure 逻辑应用](https://docs.microsoft.com/en-us/azure/logic-apps/)的详细信息。
* 详细了解 [Azure 服务总线](https://docs.microsoft.com/en-us/azure/azure-functions/)。

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png