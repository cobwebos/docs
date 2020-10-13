---
title: 混合连接的诊断日志
description: 本文概述了可用于 Azure 中继的所有活动和诊断日志。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 980f2f7a737d3f2460c17a84c472cbf56f5eb90f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87532996"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>为 Azure 中继混合连接启用诊断日志
开始使用 Azure 中继混合连接时，你可能希望监视你的侦听器和发送方是如何以及何时打开和关闭的，以及你的混合连接是如何创建的，消息是如何发送的。 本文概述了 Azure 中继服务提供的活动和诊断日志。 

可以查看两种类型的 Azure 中继日志：

- [活动日志](../azure-monitor/platform/platform-logs-overview.md)：这些日志包含的信息涉及通过 Azure 门户或 Azure 资源管理器模板对命名空间执行的操作。 这些日志始终启用。 例如：“创建或更新命名空间”、“创建或更新混合连接”。 
- [诊断日志](../azure-monitor/platform/platform-logs-overview.md)：使用 API 或语言 SDK，你可以对诊断日志进行配置，以更丰富的视图查看针对命名空间执行操作时所发生的一切。

## <a name="view-activity-logs"></a>查看活动日志
若要查看 Azure 中继命名空间的活动日志，请切换到 Azure 门户中的“活动日志”页。

![Azure 中继 - 活动日志](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>启用诊断日志

> [!NOTE]
> 诊断日志仅适用于混合连接，不适用于 Windows Communication Foundation (WCF) 中继。

若要启用诊断日志，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，转到 Azure 中继命名空间，然后在“监视”下选择“诊断设置” 。
1. 在“诊断设置”页中，选择“添加诊断设置” 。  

   ![“添加诊断设置”链接](./media/diagnostic-logs/add-diagnostic-setting.png)

1. 通过以下步骤配置诊断设置：
    1. 在“名称”框中，输入诊断设置的名称。  
    2. 选择“HybridConnectionsEvent”作为日志类型。 
    3. 为诊断日志选择以下三个目标之一：  
        1. 如果选择“存档到存储帐户”，请配置用于存储诊断日志的存储帐户。  
        2. 如果选择“流式传输到事件中心”，请配置要将诊断日志流式传输到的事件中心。
        3. 如果选择“发送到 Log Analytics”，请指定要将诊断发送到其中的 Log Analytics 实例。  

        ![示例诊断设置](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. 选择工具栏上的“保存”以保存设置。

新设置将在大约 10 分钟后生效。 日志将显示在“诊断日志”窗格中配置的存档目标中。 有关配置诊断设置的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。


## <a name="schema-for-hybrid-connections-events"></a>混合连接事件的架构
混合连接事件日志 JSON 字符串包括下表中列出的元素：

| 名称 | 描述 |
| ------- | ------- |
| ResourceId | Azure 资源管理器资源 ID |
| ActivityId | 内部 ID，用于标识指定的操作。 也称为“TrackingId” |
| 端点 | 中继资源的地址 |
| OperationName | 所记录的混合连接操作的类型 |
| EventTimeString | 日志记录的 UTC 时间戳 |
| 消息 | 事件的详细消息 |
| 类别 | 事件的类别。 目前只有 `HybridConnectionsEvents`。 


## <a name="sample-hybrid-connections-event"></a>混合连接事件示例
下面是 JSON 格式的混合连接事件示例。 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>诊断日志中捕获的事件和操作

| Operation | 说明 | 
| --------- | ----------- | 
| AuthorizationFailed | 授权失败。|
| InvalidSasToken | 无效 SAS 令牌。 | 
| ListenerAcceptingConnection | 侦听器正在接受连接。 |
| ListenerAcceptingConnectionTimeout | 正在接受连接的侦听器已超时。 |
| ListenerAcceptingHttpRequestFailed | 由于出现异常，正在接受 HTTP 请求的侦听器失败。 |
| ListenerAcceptingRequestTimeout | 正在接受请求的侦听器已超时。 |  
| ListenerClosingFromExpiredToken | 侦听器正在关闭，因为安全令牌已过期。 | 
| ListenerRejectedConnection | 侦听器已拒绝了连接。 |
| ListenerReturningHttpResponse | 侦听器正在返回 HTTP 响应。 |  
| ListenerReturningHttpResponseFailed | 侦听器正在返回带有失败代码的 HTTP 响应。 | 
 ListenerSentHttpResponse | 中继服务已收到来自侦听器的 HTTP 响应。 | 
| ListenerUnregistered | 侦听器被注销。 | 
| ListenerUnresponsive | 侦听器在返回响应时无响应。 | 
| MessageSendingToListener | 正在向侦听器发送消息。 |
| MessageSentToListener | 消息已发送到侦听器。 | 
| NewListenerRegistered | 注册了新的侦听器。 |
| NewSenderRegistering | 正在注册新的发送方。 | 
| ProcessingRequestFailed | 处理某个混合连接操作失败。 | 
| SenderConnectionClosed | 发送方连接已关闭。 |
| SenderListenerConnectionEstablished | 发送方和侦听器已成功建立连接。 |
| SenderSentHttpRequest | 发送方发送了一个 HTTP 请求。 | 


## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 中继，请参阅：

* [Azure 中继简介](relay-what-is-it.md)
* [中继混合连接入门](relay-hybrid-connections-dotnet-get-started.md)
