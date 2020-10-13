---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 0e23e537043664929bdc1a3636de359953b66db6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711156"
---
| properties | 说明 |
|:--- |:---|
|**time** | 存储收到请求时的协调世界时 (UTC) 时间。 例如：`2018/11/08 21:09:36.6900118`。|
|**resourceId** | 存储帐户的资源 ID。 例如： `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | 所请求的操作的类别。 例如：`StorageRead`、`StorageWrite` 或 `StorageDelete`。|
|**operationName** | 执行的 REST 操作的类型。 <br> 有关操作的完整列表，请参阅[“存储分析记录的操作和状态消息”主题](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 |
|**operationVersion** | 发出请求时指定的存储服务版本。 这等效于 **x-ms-version** 标头的值。 例如：`2017-04-17`。|
|**schemaVersion** | 日志的架构版本。 例如：`1.0`。|
|**statusCode** | 请求的 HTTP 状态代码。 如果请求被中断，此值可能会设置为 `Unknown`。 <br> 例如： `206` |
|**statusText** | 所请求的操作的状态。  有关状态消息的完整列表，请参阅[“存储分析记录的操作和状态消息”主题](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 在版本 2017-04-17 及更高版本中，不使用状态消息 `ClientOtherError`。 相反，此字段包含错误代码。 例如： `SASSuccess`  |
|**durationMs** | 执行所请求操作的总时间（以毫秒为单位）。 这包括读取传入请求和向请求者发送响应的时间。 例如：`12`。|
|**callerIpAddress** | 请求者的 IP 地址，包括端口号。 例如：`192.100.0.102:4362`。 |
|**correlationId** | 用来跨资源将日志关联起来的 ID。 例如：`b99ba45e-a01e-0042-4ea6-772bbb000000`。 |
|**location** | 存储帐户的位置。 例如：`North Europe`。 |
|**protocol**|操作中使用的协议。 例如：`HTTP`、`HTTPS`、`SMB` 或 `NFS`|
| **uri** | 所请求的统一资源标识符。 |