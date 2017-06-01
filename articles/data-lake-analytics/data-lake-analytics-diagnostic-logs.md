---
title: "查看 Azure Data Lake Analytics 诊断日志 | Microsoft Docs"
description: "了解如何设置和访问 Azure Data Lake Analytics 诊断日志 "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 71a839d9c076d33c404a56bf0544666dc86eaa4d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/27/2017


---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accessing diagnostic logs for Azure Data Lake Analytics（访问 Azure Data Lake Analytics 的诊断日志）

了解如何为 Data Lake Analytics 帐户启用诊断日志记录以及如何查看为帐户收集的日志。

组织可以为 Azure Data Lake Analytics 帐户启用诊断日志记录，以便收集数据访问审核跟踪。 此记录提供如下信息：

* 访问数据的用户列表。
* 数据被访问的频率。
* 在帐户中存储的数据量。

## <a name="enable-logging"></a>启用日志记录

1. 登录到新的 [Azure 门户](https://portal.azure.com)。

2. 打开 Data Lake Analytics 帐户，并从“监视部分”中选择“诊断日志”。 接下来，选择“打开诊断”。

    ![打开诊断以收集审核和请求日志](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. 从“诊断设置”中，将状态设置为“开启”并选择日志记录选项。

    ![打开诊断以收集审核和请求日志](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "启用诊断日志")

   * 将“状态”设置为“开启”，启用诊断日志记录。

   * 可选择以两种不同的方式存储/处理数据。

     * 选择“存档到存储帐户”，将日志存储到 Azure 存储帐户。 如果想要将数据存档，请使用此选项。 如果选择此选项，必须提供一个 Azure 存储帐户，以将日志保存到其中。

     * 选择“流式传输到事件中心”将日志数据流式传输到 Azure 事件中心。 具有下游处理管道来实时分析传入日志时使用此选项。 若选择此选项，必须提供要使用的 Azure 事件中心的详细信息。

     * 选择“发送到 Log Analytics”，将数据发送到 Log Analytics 服务。 如需使用 Log Analytics 收集和分析日志，请使用此选项。
   * 指定是要获取审核日志还是请求日志，或者两者都获取。

   * 指定数据必须保留的天数。

   * 单击“保存” 。

        > [!NOTE]
        > 单击“保存”按钮之前，必须选择“存档到存储帐户”、“流式传输到事件中心”或“发送到 Log Analytics”。

启用诊断设置后，可返回到“诊断日志”边栏选项卡查看日志。

## <a name="view-logs"></a>查看日志

有两种方式用于查看 Data Lake Analytics 帐户日志数据。

* 从 Data Lake Analytics 帐户设置查看
* 从存储数据的 Azure 存储帐户查看

### <a name="using-the-data-lake-analytics-settings-view"></a>使用 Data Lake Analytics 设置查看

1. 从 Data Lake Analytics 帐户边栏选项卡中，选择“诊断日志”，然后选择要显示日志的条目。

    ![查看诊断日志记录](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "查看诊断日志记录")

2. 日志按“审核日志”和“请求日志”分类。

    ![日志项](./media/data-lake-analytics-diagnostic-logs/diagnostic-log-entries.png)

   * 请求日志捕获 Data Lake Analytics 帐户上作出的每个 API 请求。
   * 审核日志类似于请求日志，但提供 Data Lake Analytics 帐户上正在执行的操作的更详细的分解结构。 例如，一个单一上传 API 调用请求日志可能导致审核日志中有多个“附加”操作。

3. 单击日志条目的“下载”链接下载日志。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>从包含日志数据的 Azure 存储帐户查看

1. 打开与日志记录的 Data Lake Analytics 关联的 Azure 存储帐户边栏选项卡，然后单击 Blob。 “Blob 服务”边栏选项卡上会列出两个容器。

    ![查看诊断日志记录](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "查看诊断日志记录")

   * 容器 **insights-logs-audit** 包含审核日志。
   * 容器 **insights-logs-requests** 包含请求日志。
2. 在这些容器中，日志存储在以下结构下。

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > 路径中的 `##` 条目包含创建日志时的年、月、日和小时。 Data Lake Analytics 每隔一小时创建一个文件，因此 `m=` 始终包含值 `00`。

    例如，审核日志的完整路径可能是：

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    类似地，请求日志的完整路径可能是：

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>日志结构

审核和请求日志格式为 JSON。 本部分介绍请求和审核日志的 JSON 结构。

### <a name="request-logs"></a>请求日志

此处是 JSON 格式的请求日志中的一个示例条目。 每个 Blob 具有一个名为 **records** 的根对象，该对象包含一组日志对象。

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>请求日志架构

| Name | 类型 | 说明 |
| --- | --- | --- |
| time |String |日志时间戳（采用 UTC） |
| resourceId |String |操作发生所在的资源的 ID |
| category |String |日志类别。 例如，“请求”。 |
| operationName |String |被记录的操作的名称。 例如，GetAggregatedJobHistory。 |
| resultType |String |操作状态，例如，200。 |
| callerIpAddress |String |作出请求的客户端 的IP 地址 |
| correlationId |String |日志的 ID。 此值可用于分组相关的日志条目 |
| identity |对象 |生成日志的标识 |
| properties |JSON |请参阅下节（请求日志属性架构），了解详细信息 |

#### <a name="request-log-properties-schema"></a>请求日志属性架构

| Name | 类型 | 说明 |
| --- | --- | --- |
| HttpMethod |String |用于此操作的 HTTP 方法。 例如 GET。 |
| 路径 |String |操作执行所在的路径 |
| RequestContentLength |int |HTTP 请求的内容长度 |
| ClientRequestId |String |唯一标识此请求的 ID |
| StartTime |String |服务器接收请求的时间 |
| EndTime |String |服务器发送响应的时间 |

### <a name="audit-logs"></a>审核日志

此处是 JSON 格式的审核日志中的一个示例条目。 每个 Blob 具有一个名为 **records** 的根对象，该对象包含一组日志对象

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>审核日志架构

| Name | 类型 | 说明 |
| --- | --- | --- |
| time |String |日志时间戳（采用 UTC） |
| resourceId |String |操作发生所在的资源的 ID |
| category |String |日志类别。 例如，“审核”。 |
| operationName |String |被记录的操作的名称。 例如，JobSubmitted。 |
| resultType |String |作业状态的字状态 (operationName)。 |
| resultSignature |String |作业状态的其他详细信息 (operationName)。 |
| identity |String |请求操作的用户。 例如，susan@contoso.com。 |
| 属性 |JSON |请参阅下节（审核日志属性架构），了解详细信息 |

> [!NOTE]
> **resultType** 和 **resultSignature** 提供操作结果信息，如果某项操作已完成，只包含一个值。 例如，当 **operationName** 包含 **JobStarted** 值或 **JobEnded** 值时，它们只包含一个值。
>
>

#### <a name="audit-log-properties-schema"></a>审核日志属性架构

| Name | 类型 | 说明 |
| --- | --- | --- |
| JobId |String |分配给作业的 ID |
| JobName |String |提供给作业的名称 |
| JobRunTime |String |用于处理作业的运行时 |
| SubmitTime |String |提交作业的时间（采用 UTC） |
| StartTime |String |作业提交后开始运行的时间（采用 UTC）。 |
| EndTime |String |作业结束时间。 |
| 并行度 |String |在提交期间为该作业请求的 Data Lake Analytics 单位数。 |

> [!NOTE]
> **SubmitTime**、**StartTime**、**EndTime** 和 **Parallelism** 提供操作信息，并且如果某一操作已开始或已完成，则只包含一个值。 例如，**operationName** 指示 **JobSubmitted** 后，**SubmitTime** 将包含一个值。

## <a name="process-the-log-data"></a>处理日志数据

Azure Data Lake Analytics 提供如何处理和分析日志数据的示例。 可在 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) 中找到该示例。

## <a name="next-steps"></a>后续步骤
* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)

