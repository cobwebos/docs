---
title: Azure 事件网格 - 为主题或域启用诊断日志
description: 本文提供了有关如何为 Azure 事件网格主题启用诊断日志的分步说明。
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: 2d76d3ededd6d241197b26ac357c3b5406f43f02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297515"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>为 Azure 事件网格主题或域启用诊断日志
诊断设置允许事件网格用户在以下任一位置捕获和查看“发布和传递失败”**** 日志：存储帐户、事件中心或 Log Analytics 工作区。 本文提供了有关如何为事件网格主题启用这些设置的分步说明。

## <a name="prerequisites"></a>先决条件

- 预配的事件网格主题
- 用于捕获诊断日志的预配目标。 它可以是事件网格主题所在位置中的以下目标之一：
    - Azure 存储帐户
    - 事件中心
    - Log Analytics 工作区

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>为自定义主题启用诊断日志

> [!NOTE]
> 以下过程提供了有关如何为主题启用诊断日志的分步说明。 为域启用诊断日志的步骤非常相似。 在步骤 2 中，导航到 Azure 门户中的事件网格域****。  

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要为其启用诊断日志设置的事件网格主题。 
    1. 在顶部的搜索栏中，搜索“事件网格主题”。 
    
        ![搜索自定义主题](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. 从列表中选择要为其配置诊断设置的**主题**。 
1. 在左侧菜单中的“监视”下，选择“诊断设置”**** ****。
1. 在“诊断设置”页面上，选择“添加新的诊断设置”。**** **** 
    
    ![“添加诊断设置”按钮](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 为诊断设置指定一个名称。**** 
6. 在“日志”**** 部分中选择 **DeliveryFailures** 和 **PublishFailures** 选项。 
    ![选择具体的失败](./media/enable-diagnostic-logs-topic/log-failures.png)
7. 为日志启用一个或多个捕获目标，然后通过选择以前创建的捕获资源对其进行配置。 
    - 如果选择“存档到存储帐户”****，请选择“存储帐户 - 配置”****，然后选择你的 Azure 订阅中的存储帐户。 

        ![屏幕截图显示 "诊断设置" 页，其中包含 "存档到 Azure 存储帐户"，并选择存储帐户。](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果选择“流式传输到事件中心”****，请选择“事件中心 - 配置”****，然后选择事件中心命名空间、事件中心和访问策略。 
        ![显示 "诊断设置" 页并选中 "流到事件中心" 的屏幕截图。](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果选择“发送到 Log Analytics”****，请选择 Log Analytics 工作区。
        ![显示 "诊断设置" 页的屏幕截图，并选中 "发送到 Log Analytics"。](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. 选择“保存” 。 然后，选择右上角的 X**** 以关闭页面。 
9. 现在，返回“诊断设置”**** 页面，确认在“诊断设置”**** 表中看到了一个新条目。 
    ![显示 "诊断设置" 页的屏幕截图，其中显示了 "诊断设置" 表中突出显示的新项。](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     你还可以为主题启用所有指标的收集。 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>为系统主题启用诊断日志

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要为其启用诊断日志设置的事件网格主题。 
    1. 在顶部的搜索栏中，搜索“事件网格系统主题”。 
    
        ![搜索系统主题](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 选择要为其配置诊断设置的**系统主题**。 
    
        ![选择系统主题](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 在左侧菜单中的“监视”下选择“诊断设置”，然后选择“添加诊断设置”。 

    ![添加诊断设置 - 按钮](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 为诊断设置指定一个名称。**** 
7. 在“日志”部分中选择“DeliveryFailures”。 
    ![选择“传递失败”](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. 为日志启用一个或多个捕获目标，然后通过选择以前创建的捕获资源对其进行配置。 
    - 如果选择“发送到 Log Analytics”****，请选择 Log Analytics 工作区。
        ![发送到 Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - 如果选择“存档到存储帐户”****，请选择“存储帐户 - 配置”****，然后选择你的 Azure 订阅中的存储帐户。 

        ![存档到 Azure 存储帐户](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - 如果选择“流式传输到事件中心”****，请选择“事件中心 - 配置”****，然后选择事件中心命名空间、事件中心和访问策略。 
        ![流式传输到事件中心](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. 选择“保存” 。 然后，选择右上角的 X**** 以关闭页面。 
9. 现在，返回“诊断设置”**** 页面，确认在“诊断设置”**** 表中看到了一个新条目。 
    ![列表中的诊断设置](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     还可以为系统主题启用收集所有指标的功能。

    ![系统主题 - 启用所有指标](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>查看 Azure 存储中的诊断日志 

1. 启用存储帐户作为捕获目标并且事件网格开始发出诊断日志后，你就会在存储帐户中看到名为 insights-logs-deliveryfailures **** 和 insights-logs-publishfailures **** 的新容器。 

    ![存储 - 诊断日志的容器](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. 当你在其中一个容器中导航时，最终将停在一个 JSON 格式的 blob 中。 该文件包含传递失败或发布失败的日志条目。 导航路径表示事件网格主题的 ResourceId **** 以及有关何时发出日志条目的时间戳（分钟级别）。 该 blob/JSON 文件（可下载）在结尾处遵循下一节中所述的架构。 

    [![存储中的 JSON 文件](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. 你应该在 JSON 文件中看到类似于以下示例的内容： 

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
有关主题或域的诊断日志的日志架构和其他概念性信息，请参阅[诊断日志](diagnostic-logs.md)。
