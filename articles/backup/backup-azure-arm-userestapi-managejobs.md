---
title: Azure 备份：使用 REST API 管理备份作业
description: 使用 REST API 管理 Azure 备份的备份和还原作业
author: pvrk
manager: shivamg
keywords: REST API、Azure VM 备份、Azure VM 还原；
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: d497fc714e0ad5f61873d4c1f95ab35837532646
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466763"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>使用 REST API 跟踪备份和还原作业

Azure 备份服务会触发在各种情况下在后台运行的作业，例如触发备份、执行还原操作以及禁用备份。 可以使用作业的 ID 跟踪这些作业。

## <a name="fetch-job-information-from-operations"></a>从操作中提取作业信息

操作（例如触发备份）将始终返回 jobID。 例如:[触发器备份 REST API 操作](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3)的最终响应如下所示:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Azure VM 备份作业由“jobId”字段予以标识，并且可以如[此处](https://docs.microsoft.com/rest/api/backup/jobdetails/)所述使用简单的 *GET* 请求进行跟踪。

## <a name="tracking-the-job"></a>跟踪作业

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

`{jobName}` 是上面提到的“jobId”。 响应始终为 200 OK，并且始终包含指示作业的当前状态的“status”字段。 当状态为“Completed”或“CompletedWithWarnings”之后，“extendedInfo”部分将显示关于作业的更多详细信息。

### <a name="response"></a>响应

|姓名  |类型  |描述  |
|---------|---------|---------|
|200 正常     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | 确定        |

#### <a name="example-response"></a>示例响应

提交 *GET* URI 后，将返回 200 (OK) 响应。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```