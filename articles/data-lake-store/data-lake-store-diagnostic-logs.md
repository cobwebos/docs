---
title: "查看 Azure Data Lake Store 诊断日志 | Microsoft Docs"
description: "了解如何设置和访问 Azure Data Lake Store 诊断日志 "
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 73d0dabe5b8b179cbc0847c2819947febd6ef4d8
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Accessing diagnostic logs for Azure Data Lake Store（访问 Azure Data Lake Store 的诊断日志）
了解如何启用 Data Lake Store 帐户诊断日志记录以及如何查看为帐户收集的日志。

组织可启用 Azure Data Lake Store 帐户诊断记录来收集数据访问审核跟踪（会提供访问数据的用户列表、数据的访问频率和帐户中存储的数据量等信息）。

## <a name="prerequisites"></a>先决条件
* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Store 帐户**。 遵循[通过 Azure 门户实现 Azure Data Lake Store 入门](data-lake-store-get-started-portal.md) 中的说明。

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>对 Data Lake Store 帐户启用诊断日志记录
1. 登录到新的 [Azure 门户](https://portal.azure.com)。
2. 打开 Data Lake Store 帐户，在 Data Lake Store 帐户边栏选项卡上单击“设置”，并单击“诊断日志”。
3. 在“诊断日志”边栏选项卡中，单击“启用诊断”。

    ![启用诊断日志记录](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "启用诊断日志")

3. 在“诊断”边栏选项卡上，作出以下更改来配置诊断日志记录。
   
    ![启用诊断日志记录](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "启用诊断日志")
   
   * 对于“名称”，请输入诊断日志配置的值。
   * 可选择以不同的方式存储/处理数据。
     
        * 选择选项“存档到存储帐户”将日志存储到 Azure 存储帐户。 要存档以后会进行批处理的数据时使用此选项。 如果选择此选项，必须提供一个要将日志保存到的 Azure 存储帐户。
        
        * 选择选项“流式传输到事件中心”将日志数据流式传输到 Azure 事件中心。 具有下游处理管道来实时分析传入日志时最可能使用此选项。 若选择此选项，必须提供要使用的 Azure 事件中心的详细信息。

        * 选择选项“发送到 Log Analytics”，以使用 Azure Log Analytics 服务分析生成的日志数据。 如果选择此选项，必须提供要用于执行日志分析的 Operations Management Suite 工作区的详细信息。
     
   * 指定是要获取审核日志还是请求日志，或者两者都获取。
   * 指定数据必须保留的天数。 保留期仅在使用 Azure 存储帐户存档日志数据时才适用。
   * 单击“保存” 。

启用诊断设置后，可在“诊断日志”选项卡中查看日志。

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>查看 Data Lake Store 帐户诊断日志
查看 Data Lake Store 帐户日志数据有两种方式。

* 从 Data Lake Store 帐户设置查看
* 从存储数据的 Azure 存储帐户

### <a name="using-the-data-lake-store-settings-view"></a>使用 Data Lake Store 设置查看
1. 从 Data Lake Store 帐户“设置”边栏选项卡，单击“诊断日志”。
   
    ![查看诊断日志记录](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "查看诊断日志") 
2. 在“诊断日志”边栏选项卡上，可看到按“审核日志”和“请求日志”分类的日志。
   
   * 请求日志捕获 Data Lake Store 帐户上作出的每个 API 请求。
   * 审核日志类似于请求日志，但提供 Data Lake Store 帐户上正在执行的操作的更详细的分解结构。 例如，一个单一上传 API 调用请求日志可能导致审核日志中有多个“附加”操作。
3. 要下载日志，请单击每个日志条目的“下载”链接。

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>从包含日志数据的 Azure 存储帐户查看
1. 打开与日志记录的 Data Lake Store 关联的 Azure 存储帐户边栏选项卡，并单击 Blob。 “Blob 服务”边栏选项卡上会列出两个容器。
   
    ![查看诊断日志记录](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "查看诊断日志记录")
   
   * 容器 **insights-logs-audit** 包含审核日志。
   * 容器 **insights-logs-requests** 包含请求日志。
2. 在这些容器中，日志存储在以下结构下。
   
    ![查看诊断日志记录](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "查看诊断日志")
   
    例如，审核日志的完整路径可能是 `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    类似地，请求日志的完整路径可能是 `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>了解日志数据的结构
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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName |String |被记录的操作的名称。 例如 getfilestatus。 |
| resultType |String |操作状态，例如，200。 |
| callerIpAddress |String |作出请求的客户端 的IP 地址 |
| correlationId |String |可用于将一组相关日志条目组合在一起的日志的 ID |
| identity |对象 |生成日志的标识 |
| properties |JSON |详细信息参见以下内容 |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName |String |被记录的操作的名称。 例如 getfilestatus。 |
| resultType |String |操作状态，例如，200。 |
| correlationId |String |可用于将一组相关日志条目组合在一起的日志的 ID |
| identity |对象 |生成日志的标识 |
| properties |JSON |详细信息参见以下内容 |

#### <a name="audit-log-properties-schema"></a>审核日志属性架构
| Name | 类型 | 说明 |
| --- | --- | --- |
| StreamName |String |操作执行所在的路径 |

## <a name="samples-to-process-the-log-data"></a>日志数据处理示例
Azure Data Lake Store 提供如何处理和分析日志数据的示例。 可在 [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) 找到该示例。 

## <a name="see-also"></a>另请参阅
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)


