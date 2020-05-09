---
title: Azure 事件网格-为主题或域启用诊断日志
description: 本文提供了有关如何为 Azure 事件网格主题启用诊断日志的分步说明。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: 13a2168c854475b841b0ebc52bb678c7ca22a1bb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626456"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>为 Azure 事件网格主题或域启用诊断日志
"诊断设置" 允许事件网格用户在存储帐户、事件中心或 Log Analytics 工作区中捕获和查看**发布和传递失败**日志。 本文提供了有关在事件网格主题上启用这些设置的分步说明。

## <a name="prerequisites"></a>必备条件

- 预配的事件网格主题
- 用于捕获诊断日志的预配目标。 它可以位于与事件网格主题相同的位置中的下列目标之一：
    - Azure 存储帐户
    - 事件中心
    - Log Analytics 工作区

## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>为主题启用诊断日志的步骤

> [!NOTE]
> 以下过程提供了有关为主题启用诊断日志的分步说明。 为域启用诊断日志的步骤非常类似。 在步骤2中，导航到 Azure 门户中的事件网格**域**。  

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 导航到要为其启用诊断日志设置的事件网格主题。 
3. 在左侧菜单中的“监视”下，选择“诊断设置”********。
4. 在“诊断设置”页面上，选择“添加新的诊断设置”。******** 
    
    ![“添加诊断设置”按钮](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 为诊断设置指定一个名称。**** 
7. 在“日志”**** 部分中选择 **DeliveryFailures** 和 **PublishFailures** 选项。 
    ![选择具体的失败](./media/enable-diagnostic-logs-topic/log-failures.png)
6. 为日志启用一个或多个捕获目标，然后通过选择以前创建的捕获资源对其进行配置。 
    - 如果选择“存档到存储帐户”****，请选择“存储帐户 - 配置”****，然后选择你的 Azure 订阅中的存储帐户。 

        ![存档到 Azure 存储帐户](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果选择“流式传输到事件中心”****，请选择“事件中心 - 配置”****，然后选择事件中心命名空间、事件中心和访问策略。 
        ![流式传输到事件中心](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果选择“发送到 Log Analytics”****，请选择 Log Analytics 工作区。
        ![发送到 Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. 选择“保存”  。 然后，在右上角选择 " **X** " 以关闭页面。 
9. 现在，返回“诊断设置”**** 页面，确认在“诊断设置”**** 表中看到了一个新条目。 
    ![列表中的诊断设置](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     你还可以为主题启用所有指标的收集。 

## <a name="view-diagnostic-logs-in-azure-storage"></a>查看 Azure 存储中的诊断日志 

1. 启用存储帐户作为捕获目标并且事件网格开始发出诊断日志后，应在存储帐户中看到名为 " **insights-deliveryfailures** " 和 " **insights-publishfailures** " 的新容器。 

    ![存储-诊断日志的容器](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. 当你在一个容器中导航时，最终将以 JSON 格式的 blob 结束。 此文件包含传递失败或发布失败的日志项。 导航路径表示事件网格主题的**ResourceId**和日志条目发出时的时间戳（分钟级别）。 Blob/JSON 文件（可下载）在结尾处遵循下一节中所述的架构。 

    [![存储](./media/enable-diagnostic-logs-topic/select-json.png)中的 JSON 文件](./media/enable-diagnostic-logs-topic/select-json.png)
3. 应会在 JSON 文件中看到类似于以下示例的内容： 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>后续步骤
有关主题或域的日志架构和诊断日志的其他概念性信息，请参阅[诊断日志](diagnostic-logs.md)。
