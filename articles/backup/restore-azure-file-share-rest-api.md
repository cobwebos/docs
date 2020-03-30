---
title: 使用 REST API 还原 Azure 文件共享
description: 了解如何使用 REST API 从 Azure 备份创建的还原点还原 Azure 文件共享或特定文件
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1c3160491ef92c62745af1468556e7d5c30437fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252501"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>使用 REST API 还原 Azure 文件共享

本文介绍如何使用 REST API 从[Azure 备份](https://docs.microsoft.com/azure/backup/backup-overview)创建的还原点还原整个文件共享或特定文件。

在本文结束时，您将了解如何使用 REST API 执行以下操作：

* 查看备份的 Azure 文件共享的还原点。
* 还原完整的 Azure 文件共享。
* 还原单个文件或文件夹。

## <a name="prerequisites"></a>先决条件

我们假设您已经拥有要还原的备份文件共享。 如果没有，请使用 REST API 检查[备份 Azure 文件共享](backup-azure-file-share-rest-api.md)，了解如何创建备份 Azure 文件共享。

在本文中，我们将使用以下资源：

* **恢复服务Vault**： *azure 文件库*
* **资源组** *：azure 文件*
* **存储帐户** *：afs 帐户*
* **文件共享**： *azure 文件*

## <a name="fetch-containername-and-protecteditemname"></a>获取容器名称和受保护项目名称

对于大多数还原相关的 API 调用，您需要传递 [容器名称] 和 [受保护 ItemName] URI 参数的值。 使用[GET 备份可保护项](https://docs.microsoft.com/rest/api/backup/protecteditems/get)操作的响应正文中的 ID 属性检索这些参数的值。 在我们的示例中，我们要保护的文件共享的 ID 是：

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

因此，这些值的转换方式如下：

* [容器名称] -*存储容器;存储;azure文件;afs 帐户*
* [受保护的项目名称] - *azure 文件共享;azure文件*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>获取备份的 Azure 文件共享的恢复点

要还原任何备份的文件共享或文件，请先选择恢复点以执行还原操作。 可以使用[恢复点列表](https://docs.microsoft.com/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems)REST API 调用列出备份项的可用恢复点。 这是一个包含所有相关值的 GET 操作。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

设置 URI 值，如下所示：

* [结构名称]： *Azure*
* [vault 名称]： *azure 文件库*
* [容器名称]：*存储容器;存储;azure文件;afs 帐户*
* [受保护的项目名称 *]：azure 文件共享;azure文件*
* [资源组名称 *]：azure文件*

GET URI 包含所有必需的参数。 不需要额外的请求正文。

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response"></a>示例响应

提交 GET URI 后，将返回 200 个响应：

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

恢复点与上述响应中的 [name] 字段一起标识。

## <a name="full-share-recovery-using-rest-api"></a>使用 REST API 进行完全共享恢复

使用此还原选项可在原始位置或备用位置还原完整的文件共享。
触发还原是 POST 请求，您可以使用[触发器还原](https://docs.microsoft.com/rest/api/backup/restores/trigger)REST API 执行此操作。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

值 [容器名称] 和 [受保护项目名称][在此处](#fetch-containername-and-protecteditemname)设置，恢复点 ID 是上述恢复点的 [name] 字段。

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>创建请求正文

要触发 Azure 文件共享的还原，以下是请求正文的组件：

“属性” |  类型   |   说明
--- | ---- | ----
属性 | Azure 文件共享还原请求 | 还原请求资源属性

有关请求正文和其他详细信息的定义的完整列表，请参阅[触发器还原 REST API 文档](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)。

### <a name="restore-to-original-location"></a>还原到原始位置

#### <a name="request-body-example"></a>请求正文示例

以下请求正文定义触发 Azure 文件共享还原所需的属性：

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>还原到备用位置

为备用位置恢复指定以下参数：

* **目标资源 Id**：还原备份内容的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **名称**：还原备份内容的目标存储帐户中的文件共享。
* **目标文件夹路径**：还原数据的文件共享下的文件夹。

#### <a name="request-body-example"></a>请求正文示例

以下请求正文将*afsaccount*存储帐户中的*azure 文件*共享还原到*afaccount1*存储帐户中的*azurefile1*文件共享。

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>响应

还原操作的触发是[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 此操作将创建另一个需要单独跟踪的操作。
它返回两个响应：创建另一个操作时为 202（已接受），当该操作完成时返回 200 （OK）。

#### <a name="response-example"></a>响应示例

提交*POST* URI 以触发还原后，初始响应为 202（已接受），包含位置标头或 Azure 异步标头。

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

然后使用位置标头或使用 GET 命令跟踪生成的操作或 Azure-Async操作标头。

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

操作完成后，它将返回 200 (OK)，以及响应正文中生成的还原作业的 ID。

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

对于备用位置恢复，响应正文将如下所示：

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

由于备份作业是长时间运行的操作，因此应该根据[使用 REST API 监视作业](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)文档中所述对其进行跟踪。

## <a name="item-level-recovery-using-rest-api"></a>使用 REST API 恢复项目级别

您可以使用此还原选项还原原始位置或备用位置中的单个文件或文件夹。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

值 [容器名称] 和 [受保护项目名称][在此处](#fetch-containername-and-protecteditemname)设置，恢复点 ID 是上述恢复点的 [name] 字段。

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>创建请求正文

要触发 Azure 文件共享的还原，以下是请求正文的组件：

“属性” |  类型   |   说明
--- | ---- | ----
属性 | Azure 文件共享还原请求 | 还原请求资源属性

有关请求正文和其他详细信息的定义的完整列表，请参阅[触发器还原 REST API 文档](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)。

### <a name="restore-to-original-location"></a>还原到原始位置

以下请求正文是还原*afs 帐户*存储帐户中的 azure*文件*共享中的*还原test.txt*文件。

创建请求正文

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location"></a>还原到备用位置

以下请求正文是将恢复*在 afs 帐户*存储帐户中的*azure 文件*共享中的*还原test.txt*文件到*afaccount1*存储帐户中的*azurefile1*文件共享的*还原数据*文件夹。

创建请求正文

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

响应的处理方式应与上面解释的[完全共享还原](#full-share-recovery-using-rest-api)相同。

## <a name="next-steps"></a>后续步骤

* 了解如何使用[Rest API 管理 Azure 文件共享备份](manage-azure-file-share-rest-api.md)。
