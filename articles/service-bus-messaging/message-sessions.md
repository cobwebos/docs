---
title: Azure Service Bus 메시지 세션 | Microsoft Docs
description: Azure Service Bus 메시지 시퀀스를 세션으로 처리합니다.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7da3c3de5074df80c676238e4d43dbd677b0a3b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720225"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>메시지 세션: FIFO(처음 들어간 것부터 사용) 

Microsoft Azure Service Bus 세션을 사용하면 관련 메시지의 무제한 시퀀스를 공동으로 순서를 지정하여 처리할 수 있습니다. Service Bus에서 FIFO 보장을 실현하려면 세션을 사용합니다. Service Bus는 메시지 간 관계의 특징에 대한 규범이 아니며 메시지 시퀀스가 시작되거나 끝나는 위치를 결정하는 특정 모델을 정의하지 않습니다.

> [!NOTE]
> Service Bus의 기본 계층에서는 세션을 지원하지 않습니다. 표준 및 프리미엄 계층은 세션을 지원합니다. 자세한 내용은 [Service Bus 가격](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

발신자는 메시지를 토픽이나 큐에 제출할 때 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 속성을 세션에 고유한 애플리케이션 정의 식별자로 설정하여 세션을 만들 수 있습니다. AMQP 1.0 프로토콜 수준에서 이 값은 *group-id* 속성에 매핑됩니다.

세션 인식 큐 또는 구독에서 세션의 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)가 있는 메시지가 하나 이상일 때 해당 세션이 존재하게 됩니다. 세션이 존재하면 세션이 언제 만료되거나 사라지는지에 대해 정의된 시간이나 API가 없습니다. 이론적으로 메시지는 오늘 세션에서 수신되고 1년 후 다음 메시지가 수신될 수 있으며 **SessionId**가 일치하면 세션은 Service Bus 측면에서 동일합니다.

하지만 일반적으로 애플리케이션에는 관련된 메시지 집합이 시작되고 끝나는 위치에 대한 명확한 개념이 있지만 Service Bus는 특정 규칙을 설정하지 않습니다.

파일 전송 시퀀스를 서술하는 방법의 예에는 첫 번째 메시지의 **Label** 속성을 **start**로 설정하고 중간 메시지는 **content**로 설정하고 마지막 메시지는 **end**라고 설정하는 방법이 있습니다. 콘텐츠 메시지의 상대적인 위치는 **start** 메시지의 *SequenceNumber*에서 현재 메시지의 *SequenceNumber* 델타로 계산될 수 있습니다.

Service Bus의 세션 기능을 사용하면 C# 및 Java API의 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 형태로 특정 수신 작업이 가능합니다. 이 기능은 Azure Resource Manager를 통해 큐 또는 구독에 대한 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 속성을 설정하거나 포털에서 플래그를 설정하여 사용하도록 설정합니다. 이 단계는 관련 API 작업을 사용하기 전에 필요합니다.

포털에서 다음 확인란을 사용하여 플래그를 지정합니다.

![][2]

> [!NOTE]
> 在队列或订阅上启用会话时，客户端应用程序无法***再***发送/接收常规消息。 所有消息都必须作为会话的一部分进行发送（通过设置会话 id）并接收会话接收。

세션용 API는 큐 및 구독 클라이언트에 있습니다. 세션 및 메시지가 수신되는 시간을 제어하는 필수 모델과 수신 루프 관리의 복잡성을 숨기는 *OnMessage*와 유사한 처리기 기반 모델이 있습니다.

## <a name="session-features"></a>세션 기능

세션은 순차적 전달을 보존하고 보장하면서 인터리브된 메시지 스트림에 대한 동시 역멀티플렉싱을 제공합니다.

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 수신기는 세션을 허용하는 클라이언트에서 생성됩니다. 클라이언트는 C#의 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 또는 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)를 호출합니다. 반응성 콜백 모델에서는 세션 처리기를 등록합니다.

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 개체가 수락되고 클라이언트에서 유지되는 동안 해당 클라이언트는 큐 또는 구독에 있는 세션의 [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId)가 포함된 모든 메시지와 세션이 유지되는 동안 도착하는 **SessionId**가 포함된 모든 메시지에 대해 배타적 잠금을 유지합니다.

잠금은 **Close** 또는 **CloseAsync**가 호출되거나 애플리케이션이 잠금 작업을 수행할 수 없어서 잠금이 만료될 때 해제됩니다. 세션 잠금은 파일에 대한 배타적인 잠금으로 처리되어야 합니다. 즉, 애플리케이션은 세션이 더 이상 필요하지 않거나 더 이상 메시지를 기대하지 않는 경우 세션을 닫아야 합니다.

다수의 동시 수신자가 큐에서 풀링되면 특정 세션에 속하는 메시지는 해당 세션에 대한 잠금을 보유하고 있는 특정 수신자에게 전달됩니다. 이러한 작업을 통해 하나의 큐 또는 구독에 상주하는 인터리브된 메시지 스트림이 다른 수신자로 완전히 역멀티플렉싱되며 잠금 관리가 Service Bus 내의 서비스 쪽에서 발생하기 때문에 해당 수신자는 다른 클라이언트 시스템에서도 작동할 수 있습니다.

앞의 그림에는 3개의 동시 세션 수신기가 나와 있습니다. `SessionId` = 4인 한 세션에 활성, 소유 클라이언트가 없습니다. 즉, 이 특정 세션에서 전달된 메시지가 없습니다. 세션은 하위 큐와 같이 여러 가지 방법으로 작동합니다.

세션 수신자가 보유한 세션 잠금은 *peek-lock* 정산 모드에서 사용되는 메시지 잠금에 대한 우산입니다. 수신자는 "진행 중" 동시에 두 개의 메시지를 가질 수 없지만 메시지는 순서대로 처리되어야 합니다. 이전 메시지가 완료되거나 배달 못 한 편지로 처리된 경우에만 새 메시지를 얻을 수 있습니다. 메시지를 버리면 다음 수신 작업으로 동일한 메시지가 다시 제공됩니다.

## <a name="message-session-state"></a>메시지 세션 상태

워크플로가 대규모의 고가용성 클라우드 시스템에서 처리되는 경우 특정 세션과 연관된 워크플로 처리기는 예상치 못한 장애를 복구할 수 있어야 하며 작업이 시작된 다른 프로세스 또는 컴퓨터에서 부분적으로 완료된 작업을 재개할 수 있어야 합니다.

세션 상태 기능을 통해 broker 내부의 메시지 세션에 대한 애플리케이션 정의 주석을 사용할 수 있기 때문에 세션을 새 프로세서로 가져올 때 해당 세션에 대해 기록된 처리 상태를 즉시 사용할 수 있습니다.

Service Bus 측면에서 메시지 세션 상태는 한 개 메시지 크기의 데이터(Service Bus Standard의 경우 256KB 및 Service Bus Premium의 경우 1MB)를 보유할 수 있는 불투명한 이진 개체입니다. 세션과 관련된 처리 상태는 세션 상태 내에 유지되거나 이러한 정보를 보유하는 스토리지 위치 또는 데이터베이스 레코드를 세션 상태가 가리킬 수 있습니다.

세션 상태를 관리하는 API, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 및 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)는 C# 및 Java API의 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 개체에서 찾을 수 있습니다. 이전에 세션 상태가 설정되지 않은 세션은 **GetState**에 대한 **null** 참조를 반환합니다. 이전에 설정된 세션 상태를 지우는 것은 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_)을 통해 수행됩니다.

세션의 모든 메시지를 사용한 경우에도 지우지 않는 한 세션 상태는 유지됩니다(**null** 반환).

큐 또는 구독의 모든 기존 세션은 Java API의 **SessionBrowser** 메서드 및 .NET 클라이언트의 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 및 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient)의 [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions)를 사용하여 열거할 수 있습니다.

큐 또는 구독에 보관된 세션 상태는 해당 엔터티의 스토리지 할당량에 포함됩니다. 애플리케이션이 세션을 끝내면 외부 관리 비용을 피하기 위해 애플리케이션이 보유 상태를 정리하는 것이 좋습니다.

## <a name="impact-of-delivery-count"></a>传递计数的影响

会话上下文中每条消息的传递数定义与缺少会话的定义略有不同。 下面是一个表，汇总了传递计数增加的时间。

| 시나리오 | 消息的传递计数是否递增 |
|----------|---------------------------------------------|
| 会话被接受，但会话锁过期（由于超时） | 예 |
| 会话被接受，会话中的消息不会完成（即使它们已被锁定），会话也会关闭 | 아닙니다. |
| 接受会话，消息完成，然后显式关闭会话 | 不适用（这是标准流。 此处的消息将从会话中删除） |

## <a name="next-steps"></a>다음 단계

- 有关使用 .NET Framework 的客户端处理会话感知消息[的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)，请参阅[microsoft. Azure](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ..。 

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
