---
title: Azure 服务总线到事件网格集成概述 | Microsoft Docs
description: 服务总线消息传送和事件网格集成的说明
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
ms.openlocfilehash: 1da2b6568125c61db2c4dda3bd81abb8f1a59ec7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Azure 服务总线到事件网格的集成概述

Azure 服务总线已推出与 Azure 事件网格集成的新功能。 此功能实现的重要方案是，消息量不大的服务总线队列或订阅不需要一直使用接收器来轮询消息。 

现在，当队列或订阅中存在消息时，服务总线可以在没有接收器的情况下，将事件发出到事件网格。 可以针对服务总线命名空间创建事件网格订阅、侦听这些事件，然后通过启动接收器对这些事件做出反应。 借助此功能，可以在反应式编程模型中使用服务总线。

若要启用此功能，需要准备好以下各项：

* 至少包含一个服务总线队列的服务总线高级命名空间，或者至少包含一个订阅的服务总线主题。
* 对服务总线命名空间拥有“参与者”访问权限。
* 此外，需要服务总线命名空间的事件网格订阅。 存在可提取的消息时，此订阅将从事件网格接收通知。 典型的订阅方可能是 Azure 应用服务的逻辑应用功能、Azure Functions，或者与 Web 应用联系的 Webhook。 然后，订阅方会处理消息。 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>验证你是否有参与者访问权限

转到服务总线命名空间，选择“访问控制(IAM)”，如下所示：

![1][]

### <a name="events-and-event-schemas"></a>事件和事件架构

目前，Azure 服务总线会针对两种情况发送事件：

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

此外，服务总线使用标准的事件网格安全性和[身份验证机制](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication)。

有关详细信息，请参阅 [Azure 事件网格事件架构](https://docs.microsoft.com/en-us/azure/event-grid/event-schema)。

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

#### <a name="dead-letter-messages-available-event"></a>死信消息可用事件

对于每个有消息但没有活动接收器的死信队列，你会至少收到一个事件。

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

### <a name="how-many-events-are-emitted-and-how-often"></a>发出事件的数量和频率如何？

如果命名空间中有多个队列和主题或订阅，则对于每个队列和每个订阅，你会至少收到一个事件。 如果服务总线实体中没有任何消息，在新消息抵达时，会立即发出事件。 或者，除非服务总线检测到活动的接收方，否则每隔两分钟发送事件。 对消息进行浏览不会导致事件中断。

默认情况下，服务总线针对命名空间中的所有实体发出事件。 如果只需获取特定实体的事件，请参阅下一部分。

### <a name="use-filters-to-limit-where-you-get-events-from"></a>使用筛选器限制事件的来源

例如，如果你只想从命名空间中的某个队列或订阅接收事件，可以使用事件网格提供的“开头为”或“结尾为”筛选器。 在某些界面中，这两个筛选器称为“前缀”和“后缀”筛选器。 如果需要接收多个（但并非所有）队列和订阅的事件，可以创建多个事件网格订阅，并为每个订阅提供一个筛选器。

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>为服务总线命名空间创建事件网格订阅

可以使用三种不同的方法，为服务总线命名空间创建事件网格订阅：

* 在 [Azure 门户](#portal-instructions)中
* 在 [Azure CLI](#azure-cli-instructions) 中
* 在 [PowerShell](#powershell-instructions) 中

## <a name="azure-portal-instructions"></a>Azure 门户说明

若要创建新的事件网格订阅，请执行以下操作：
1. 在 Azure 门户中转到自己的命名空间。
2. 在左窗格中，选择“事件网格”。 
3. 选择“事件订阅”。  

   下图显示了包含一些事件网格订阅的命名空间：

   ![20][]

   下图演示了如何在不使用任何特定筛选器的情况下订阅函数或 Webhook：

   ![21][]

## <a name="azure-cli-instructions"></a>Azure CLI 说明

首先，请确保已安装 Azure CLI 2.0 或更高版本。 [下载安装程序](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)。 按 **Windows + X**，然后使用管理员权限打开新的 PowerShell 控制台。 或者，也可以在 Azure 门户中使用命令外壳。

执行以下代码：

 ```azurecli-interactive
az login

az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell 说明

确保已安装 Azure PowerShell。 [下载安装程序](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.4.0)。 按 **Windows + X**，然后使用管理员权限打开新的 PowerShell 控制台。 或者，也可以在 Azure 门户中使用命令外壳。

```PowerShell-interactive
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

可以在这里浏览其他设置选项，或者[测试事件是否正在流动](#test-that-events-are-flowing)。

## <a name="next-steps"></a>后续步骤

* 获取服务总线和事件网格的[示例](service-bus-to-event-grid-integration-example.md)。
* 详细了解[事件网格](https://docs.microsoft.com/en-us/azure/azure-functions/)。
* 详细了解 [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/)。
* 了解有关[逻辑应用](https://docs.microsoft.com/en-us/azure/logic-apps/)的详细信息。
* 详细了解[服务总线](https://docs.microsoft.com/en-us/azure/azure-functions/)。

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
