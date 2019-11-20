---
title: 使用存储分析日志排查延迟问题
description: 使用 Azure 存储分析日志来识别和解决延迟问题，并优化客户端应用程序。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196511"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>使用存储分析日志排查延迟问题

诊断和故障排除是生成和支持使用 Azure 存储的客户端应用程序的关键技能。

由于 Azure 应用程序的分布式特性，诊断和排查错误与性能问题可能会比在传统环境中更复杂。

以下步骤演示如何使用 Azure 存储分析日志来识别和排查延迟问题，并优化客户端应用程序。

## <a name="recommended-steps"></a>建议的步骤

1. 下载[存储分析日志](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)。

2. 使用以下 PowerShell 脚本将原始格式日志转换为表格格式：

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. 该脚本将启动一个 GUI 窗口，您可以在其中按列筛选信息，如下所示。

   ![存储分析日志分析器窗口](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. 根据 "操作类型" 缩小日志条目的范围，并查找在问题时间范围内创建的日志条目。

   ![操作类型日志条目](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 发生此问题时，以下值很重要：

   * 操作类型 = GetBlob
   * 请求状态 = SASNetworkError
   * 端到端延迟-毫秒 = 8453
   * 服务器延迟-毫秒 = 391

   使用以下公式计算端到端延迟：

   * 端到端延迟 = 服务器延迟 + 客户端延迟

   使用日志条目计算客户端延迟：

   * 客户端延迟 = 端到端延迟–服务器延迟

          * Example: 8453 – 391 = 8062ms

   下表提供了 OperationType 和 RequestStatus 结果的高延迟的相关信息：

   |   |RequestStatus =<br>成功|RequestStatus =<br>块NetworkError|建议|
   |---|---|---|---|
   |GetBlob|是|否|[**GetBlob 操作：** RequestStatus = Success](#getblob-operation-requeststatus--success)|
   |GetBlob|否|是|[**GetBlob 操作：** RequestStatus = （SAS） NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|是|否|[**Put 操作：** RequestStatus = Success](#put-operation-requeststatus--success)|
   |PutBlob|否|是|[**Put 操作：** RequestStatus = （SAS） NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>状态结果

### <a name="getblob-operation-requeststatus--success"></a>GetBlob 操作： RequestStatus = Success

如 "建议步骤" 部分的步骤5中所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端-延迟

在**RequestStatus = Success**的**GetBlob 操作**中，如果在**客户端延迟**时间内花费了**最长时间**，则这表明 Azure 存储将花费大量时间向客户端写入数据。 此延迟表示客户端问题。

**建议：**

* 调查客户端中的代码。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob 操作： RequestStatus = （SAS） NetworkError

如 "建议步骤" 部分的步骤5中所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端-延迟

在带有**RequestStatus = （SAS） NetworkError**的**GetBlob 操作**中，如果**客户端延迟**中花费了**最长时间**，则最常见的问题是客户端在存储服务超时到期之前断开连接。

**建议：**

* 应调查客户端中的代码，以了解客户端断开与存储服务的连接的原因和时间。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。 

### <a name="put-operation-requeststatus--success"></a>Put 操作： RequestStatus = Success

如 "建议步骤" 部分的步骤5中所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端-延迟

在**RequestStatus = Success**的**Put 操作**中，如果在**客户端延迟**时间内花费了**最长时间**，则表示客户端将花费更多的时间将数据发送到 Azure 存储。 此延迟表示客户端问题。

**建议：**

* 调查客户端中的代码。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put 操作： RequestStatus = （SAS） NetworkError

如 "建议步骤" 部分的步骤5中所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端-延迟

在带有**RequestStatus = （SAS） NetworkError**的**PutBlob 操作**中，如果**客户端延迟**中花费了**最长时间**，则最常见的问题是客户端在存储服务超时到期之前断开连接。

**建议：**

* 应调查客户端中的代码，以了解客户端断开与存储服务的连接的原因和时间。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。

