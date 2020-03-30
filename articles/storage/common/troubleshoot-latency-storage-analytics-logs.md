---
title: 使用存储分析日志排查延迟问题
description: 使用 Azure 存储分析日志来查明并解决延迟问题，并优化客户端应用程序。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196511"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>使用存储分析日志排查延迟问题

诊断和故障排除是生成和支持使用 Azure 存储的客户端应用程序的关键技能。

由于 Azure 应用程序是分布式的，因此对错误与性能问题进行诊断和故障排除可能会比在传统环境中更为复杂。

以下步骤演示了如何使用 Azure 存储分析日志来查明并解决延迟问题，并优化客户端应用程序。

## <a name="recommended-steps"></a>建议的步骤

1. 下载[存储分析日志](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)。

2. 使用以下 PowerShell 脚本将原始格式的日志转换为表格格式：

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

3. 该脚本将启动一个 GUI 窗口，你可以在其中按列筛选信息，如下所示。

   ![存储分析日志分析程序窗口](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. 根据"操作类型"缩小日志条目范围，并查找在问题时间范围内创建的日志条目。

   ![Operation-type 日志条目](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 在发生问题的时间范围内，以下值很重要：

   * Operation-type = GetBlob
   * request-status = SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Server-Latency-In-Ms = 391

   端到端延迟是使用以下公式计算的：

   * 端到端延迟 = 服务器延迟 + 客户端延迟

   使用日志条目计算客户端延迟：

   * 客户端延迟 = 端到端延迟 – 服务器延迟

          * Example: 8453 – 391 = 8062ms

   下表提供了有关高延迟 OperationType 和 RequestStatus 结果的信息：

   |   |RequestStatus=<br>Success|RequestStatus=<br>(SAS)NetworkError|建议|
   |---|---|---|---|
   |GetBlob|是|否|[**获取Blob操作：** 请求状态 = 成功](#getblob-operation-requeststatus--success)|
   |GetBlob|否|是|[**获取Blob操作：** 请求状态 = （SAS） 网络错误](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|是|否|[**放置操作：** 请求状态 = 成功](#put-operation-requeststatus--success)|
   |PutBlob|否|是|[**放置操作：** 请求状态 = （SAS） 网络错误](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>状态结果

### <a name="getblob-operation-requeststatus--success"></a>获取 Blob 操作：请求状态 = 成功

按“建议的步骤”部分中的步骤 5 所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端延迟

在 **RequestStatus = Success** 的 **GetBlob 操作**中，如果在 **Client-Latency** 中花费了 **Max Time**，则这表明 Azure 存储在将数据写入到客户端时花费了大量时间。 此延迟表明客户端存在问题。

**建议：**

* 在客户端中调查代码。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>获取Blob操作：请求状态 = （SAS）网络错误

按“建议的步骤”部分中的步骤 5 所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端延迟

在 **RequestStatus = (SAS)NetworkError** 的 **GetBlob 操作**中，如果在 **Client-Latency** 中花费了 **Max Time**，则最常见的问题是客户端在存储服务超时之前断开连接。

**建议：**

* 应调查客户端中的代码，以了解客户端断开与存储服务的连接的原因和时间。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。 

### <a name="put-operation-requeststatus--success"></a>放置操作：请求状态 = 成功

按“建议的步骤”部分中的步骤 5 所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端延迟

在 **RequestStatus = Success** 的 **Put 操作**中，如果在 **Client-Latency** 中花费了 **Max Time**，则这表明客户端在将数据发送到 Azure 存储时花费了更多时间。 此延迟表明客户端存在问题。

**建议：**

* 在客户端中调查代码。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>放置操作：请求状态 = （SAS）网络错误

按“建议的步骤”部分中的步骤 5 所述检查以下值：

* 端到端延迟
* 服务器延迟
* 客户端延迟

在 **RequestStatus = (SAS)NetworkError** 的 **PutBlob 操作**中，如果在 **Client-Latency** 中花费了 **Max Time**，则最常见的问题是客户端在存储服务超时之前断开连接。

**建议：**

* 应调查客户端中的代码，以了解客户端断开与存储服务的连接的原因和时间。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 调查客户端的网络连接问题。

