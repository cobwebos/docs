---
title: 将 Azure 活动日志收集到不同订阅中的 Log Analytics | Microsoft Docs
description: 使用事件中心和逻辑应用从 Azure 活动日志中收集数据，并将其发送到不同租户中的 Azure Log Analytics 工作区。
services: log-analytics, logic-apps, event-hubs
documentationcenter: ''
author: richrundmsft
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2018
ms.author: richrund; bwren
ms.openlocfilehash: 434cbdca42e4287a0f3d7e3960bc0baa373bc358
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions"></a>将 Azure 活动日志收集到不同订阅中的 Log Analytics

本文逐步讲解如何使用逻辑应用的 Azure Log Analytics 数据收集器连接器，将 Azure 活动日志收集到 Log Analytics 工作区。 需要将日志发送到不同 Azure Active Directory 中的工作区时，可以使用本文中所述的过程。 例如，如果你是托管服务提供商，可能想要从客户的订阅中收集活动日志，并将其存储在自己订阅中的 Log Analytics 工作区中。

如果 Log Analytics 工作区位于相同的 Azure 订阅中，或者位于不同的订阅但相同的 Azure Active Directory 中，请使用 [Azure 活动日志解决方案](../log-analytics/log-analytics-activity.md)中的步骤收集 Azure 活动日志。

## <a name="overview"></a>概述

此方案中使用的策略是让 Azure 活动日志将事件发送到某个[事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)，其中的某个[逻辑应用](../logic-apps/logic-apps-overview.md)会将这些事件发送到 Log Analytics 工作区。 

![从活动日志到 Log Analytics 的数据流示意图](media/log-analytics-activity-logs-subscriptions/data-flow-overview.png)

此方法的优点包括：
- 延迟较低，因为 Azure 活动日志会流式传输到事件中心。  逻辑应用随后将被触发并将数据发布到 Log Analytics。 
- 只需编写极少量的代码，不需要部署服务器基础结构。

本文逐步讲解如何：
1. 创建事件中心。 
2. 使用 Azure 活动日志导出配置文件将活动日志导出到事件中心。
3. 创建一个逻辑应用，用于从事件中心读取数据并将事件发送到 Log Analytics。

## <a name="requirements"></a>要求
下面是此方案中使用的 Azure 资源的要求。

- 事件中心命名空间不一定要与发出日志的订阅位于同一个订阅中。 配置此设置的用户必须对上述两个订阅拥有相应的访问权限。 如果在同一个 Azure Active Directory 中部署了多个订阅，可将所有订阅的活动日志发送到单个事件中心。
- 逻辑应用所在的订阅可与事件中心不同，并且它们不需要位于同一个 Azure Active Directory 中。 逻辑应用使用事件中心的共享访问密钥从事件中心读取数据。
- Log Analytics 工作区所在的订阅和 Azure Active Directory 可与逻辑应用不同，但为了简单起见，我们建议将它们放在同一个订阅中。 逻辑应用使用 Log Analytics 工作区 ID 和密钥将数据发送到 Log Analytics。



## <a name="step-1---create-an-event-hub"></a>步骤 1 - 创建事件中心

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../event-hubs/event-hubs-create.md) to create your event hub. -->

1. 在 Azure 门户中，选择“创建资源” > “物联网” > “事件中心”。

   ![Marketplace - 新建事件中心](media/log-analytics-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. 在“创建命名空间”下，输入新命名空间或选择现有的命名空间。 系统会立即检查该名称是否可用。

   ![创建事件中心对话框的插图](media/log-analytics-activity-logs-subscriptions/create-event-hub1.png)

4. 选择新资源的定价层（“基本”或“标准”）、Azure 订阅、资源组和位置。  单击“创建”  创建命名空间。 可能需要等待几分钟让系统完全预配资源。
6. 在列表中单击刚刚创建的命名空间。
7. 选择“共享访问策略”，然后单击“RootManageSharedAccessKey”。

   ![事件中心共享访问策略的插图](media/log-analytics-activity-logs-subscriptions/create-event-hub7.png)
   
8. 单击复制按钮，将 **RootManageSharedAccessKey** 连接字符串复制到剪贴板。 

   ![事件中心共享访问密钥的插图](media/log-analytics-activity-logs-subscriptions/create-event-hub8.png)

9. 将事件中心名称以及事件中心的主要或辅助连接字符串复制到某个临时位置（例如记事本）。 逻辑应用需要这些值。  对于事件中心连接字符串，可以使用 **RootManageSharedAccessKey** 连接字符串，或单独创建一个连接字符串。  使用的连接字符串必须以 `Endpoint=sb://` 开头，并适用于具有“管理”访问策略的策略。


## <a name="step-2---export-activity-logs-to-event-hub"></a>步骤 2 - 将活动日志导出到事件中心

若要启用活动日志的流式传输，可以选择一个事件中心命名空间，以及该命名空间的共享访问策略。 发生第一个新的活动日志事件时，将在该命名空间中创建事件中心。 

可以使用与发出日志的订阅不在同一个订阅中的事件中心命名空间，但是，这两个订阅必须位于同一个 Azure Active Directory 中。 配置此设置的用户必须对上述两个订阅拥有相应的 RBAC 访问权限。 

1. 在 Azure 门户中，选择“Monitor” > “活动日志”。
3. 单击页面顶部的“导出”按钮。

   ![导航窗格中的 Azure Monitor 插图](media/log-analytics-activity-logs-subscriptions/activity-log-blade.png)

4. 选择要从中导出的**订阅**，然后单击“区域”下拉列表中的“全选”，以选择所有区域中的资源的事件。 单击“导出到事件中心”复选框。
7. 单击“服务总线命名空间”，然后选择包含该事件中心的**订阅**、**事件中心命名空间**和**事件中心策略名称**。

    ![“导出到事件中心”页的插图](media/log-analytics-activity-logs-subscriptions/export-activity-log2.png)

11. 单击“确定”，然后单击“保存”以保存这些设置。 这些设置会即时应用到订阅。

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>步骤 3 - 创建逻辑应用

开始将活动日志写入事件中心后，可以创建一个逻辑应用，用于从事件中心收集日志并将其写入 Log Analytics。

逻辑应用中包括：
- 一个[事件中心连接器](https://docs.microsoft.com/connectors/eventhubs/)触发器，用于从事件中心读取数据。
- 一个[“分析 JSON”操作](../logic-apps/logic-apps-content-type.md)，用于提取 JSON 事件。
- 一个[“撰写”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)，用于将 JSON 转换为对象。
- 一个 [Log Analytics 发送数据连接器](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/)，用于将数据发布到 Log Analytics。

   ![在逻辑应用中添加事件中心触发器的插图](media/log-analytics-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>逻辑应用要求
在创建逻辑应用之前，请确保已准备好在前面步骤中获取的以下信息：
- 事件中心名称
- 事件中心命名空间的事件中心连接字符串（主要或辅助）。
- Log Analytics 工作区 ID
- Log Analytics 共享密钥

若要获取事件中心名称和连接字符串，请遵循[检查事件中心命名空间权限和查找连接字符串](../connectors/connectors-create-api-azure-event-hubs.md#connect-to-azure-event-hubs)中的步骤。


### <a name="create-a-new-blank-logic-app"></a>创建新的空白逻辑应用

1. 在 Azure 门户中，选择“创建资源” > “企业集成” > “逻辑应用”。

    ![Marketplace - 新建逻辑应用](media/log-analytics-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. 输入下表中所示的设置。

    ![创建逻辑应用](media/log-analytics-activity-logs-subscriptions/create-logic-app.png)

   |设置 | 说明  |
   |:---|:---|
   | 名称           | 逻辑应用的唯一名称。 |
   | 订阅   | 选择将要包含该逻辑应用的 Azure 订阅。 |
   | 资源组 | 为逻辑应用选择现有的 Azure 资源组或创建新的资源组。 |
   | Location       | 选择用于部署逻辑应用的数据中心区域。 |
   | Log Analytics  | 如果想要将逻辑应用的每次运行状态记录到 Log Analytics 中，请选择此项。  |

    
3. 选择**创建**。 显示“部署成功”通知时，请单击“转到资源”打开逻辑应用。

4. 在“模板”下选择“空白逻辑应用”。 

此时，逻辑应用设计器会显示可用的连接器及其触发器，用于启动逻辑应用工作流。

<!-- Learn [how to create a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>添加事件中心触发器

1. 在逻辑应用设计器的搜索框中，键入“事件中心”作为筛选器。 选择触发器“事件中心 - 当事件在事件中心可用时”。

   ![在逻辑应用中添加事件中心触发器的插图](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. 系统提示输入凭据时，请连接到事件中心命名空间。 依次输入连接名称和复制的连接字符串。  选择**创建**。

   ![在逻辑应用中添加事件中心连接的插图](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. 创建连接后，请编辑触发器的设置。 首先，从“事件中心名称”下拉列表中选择“insights-operational-logs”。

   ![当事件可用时对话框](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. 展开“显示高级选项”，将“内容类型”更改为 *application/json*

   ![事件中心配置对话框](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>添加“分析 JSON”操作

事件中心提供的输出包含一个带有记录数组的 JSON 有效负载。 使用[分析 JSON](../logic-apps/logic-apps-content-type.md) 操作只能提取要发送到 Log Analytics 的记录数组。

1. 单击“新建步骤” > “添加操作”。
2. 在搜索框中，键入“分析 JSON”作为筛选器。 选择“数据操作 - 分析 JSON”操作。

   ![在逻辑应用中添加“分析 JSON”操作](media/log-analytics-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. 单击“内容”字段，然后选择“正文”。

4. 将以下架构复制并粘贴到“架构”字段中。  此架构将匹配事件中心操作提供的输出。  

   ![“分析 JSON”对话框](media/log-analytics-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> 可以通过单击“运行”并查看事件中心提供的“原始输出”来获取示例有效负载。  然后，可以在“分析 JSON”活动中结合使用此输出与“使用示例有效负载生成架构”来生成架构。

### <a name="add-compose-action"></a>添加“撰写”操作
[撰写](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)操作提取 JSON 输出，并创建可供 Log Analytics 操作使用的对象。

1. 单击“新建步骤” > “添加操作”。
2. 键入“撰写”作为筛选器，然后选择“数据操作 - 撰写”操作。

    ![添加“撰写”操作](media/log-analytics-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. 单击“输入”字段并选择“分析 JSON”活动下的“正文”。


### <a name="add-log-analytics-send-data-action"></a>添加 Log Analytics 发送数据操作
[Azure Log Analytics 数据收集器](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/)操作提取“撰写”操作中的对象，并将其发送到 Log Analytics。

1. 单击“新建步骤” > “添加操作”。
2. 键入 *log analytics* 作为筛选器，然后选择“Azure Log Analytics 数据收集器 - 发送数据”操作。

   ![在逻辑应用中添加 Log Analytics 发送数据操作](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. 输入连接名称，并粘贴 Log Analytics 工作区的**工作区 ID** 和**工作区密钥**。  单击“创建”。

   ![在逻辑应用中添加 Log Analytics 连接](media/log-analytics-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. 创建连接后，请编辑下表中所示的设置。 

    ![配置发送数据操作](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |设置        | 值           | 说明  |
   |---------------|---------------------------|--------------|
   |JSON 请求正文  | **撰写**操作提供的**输出** | 从“撰写”操作的正文中检索记录。 |
   | 自定义日志名称 | AzureActivity | 在 Log Analytics 中创建的、用于保存导入数据中的自定义日志表的名称。 |
   | Time-generated-field | time | 不要选择 **time** 对应的 JSON 字段 - 只需键入世界时间。 如果选择该 JSON 字段，则设计器会在 *For Each* 循环中插入“发送数据”数据，而这不是我们想要的。 |




10. 单击“保存”，保存对逻辑应用所做的更改。

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>步骤 4 - 对逻辑应用进行测试和故障排除
完成工作流后，可以在设计器中进行测试，以验证该工作流是否正常运行而不会出错。

在逻辑应用设计器中，单击“运行”以测试逻辑应用。 逻辑应用中的每个步骤会显示状态图标，绿色圆圈中包含白色勾选标记表示成功。

   ![测试逻辑应用](media/log-analytics-activity-logs-subscriptions/test-logic-app.png)

若要查看有关每个步骤的详细信息，请单击该步骤的名称将其展开。 单击“显示原始输入”和“显示原始输出”可以查看有关每个步骤接收和发送的数据的详细信息。

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>步骤 5 - 在 Log Analytics 中查看 Azure 活动日志
最后一步是检查 Log Analytics 工作区，以确保按预期收集数据。

1. 在 Azure 门户中，单击左上角的“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 工作区列表中选择工作区。
3.  单击“日志搜索”磁贴并在“日志搜索”窗格上的查询字段中键入 `AzureActivity_CL`，然后按 Enter 或单击查询字段右侧的搜索按钮。 如果未将自定义日志命名为 *AzureActivity*，请键入所选的名称，并在其后面追加 `_CL`。

>[!NOTE]
> 首次将新的自定义日志发送到 Log Analytics 后，最长可能需要经过一小时，该自定义日志才可供搜索。

>[!NOTE]
> 活动日志将写入自定义表，而不会显示在[活动日志解决方案](./log-analytics-activity.md)中。


![测试逻辑应用](media/log-analytics-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>后续步骤

本文已介绍如何创建一个逻辑应用，用于从事件中心读取 Azure 活动日志并将其发送到 Log Analytics 进行分析。 若要详细了解如何可视化 Log Analytics 中的数据（包括创建仪表板），请查看有关可视化数据的教程。

> [!div class="nextstepaction"]
> [有关可视化日志搜索数据的教程](./log-analytics-tutorial-dashboards.md)
