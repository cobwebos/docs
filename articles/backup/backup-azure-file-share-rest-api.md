---
title: 使用 REST API 备份 Azure 文件共享
description: 了解如何使用 REST API 在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248094"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>通过休息 API 使用 Azure 备份备份 Azure 文件共享

本文介绍如何使用通过 REST API 的 Azure 备份备份 Azure 文件共享。

本文假定您已经创建了一个恢复服务保管库和策略，用于为文件共享配置备份。 如果没有，请参阅[创建保管库](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault)并[创建策略](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)REST API 教程以创建新的保管库和策略。

在本文中，我们将使用以下资源：

- **恢复服务Vault**： *azure 文件库*

- **策略：***计划1*

- **资源组** *：azure 文件*

- **存储帐户**：*测试库2*

- **文件共享**：*测试共享*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>使用 REST API 为未受保护的 Azure 文件共享配置备份

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>发现具有未受保护的 Azure 文件共享的存储帐户

保管库需要发现订阅中的所有 Azure 存储帐户，并包含可备份到恢复服务保管库的文件共享。 这是使用[刷新操作](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)触发的。 它是一个异步*POST*操作，可确保保管库获取当前订阅中所有未受保护的 Azure 文件共享的最新列表，并"缓存"它们。 文件共享被"缓存"后，恢复服务可以访问文件共享并保护它。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI`{subscriptionId}`具有`{vaultName}` `{vaultresourceGroupName}`、和`{fabricName}`参数。 在我们的示例中，不同参数的值如下所示：

- `{fabricName}`是*Azure*

- `{vaultName}`是*azure 文件库*

- `{vaultresourceGroupName}`是*azure 文件*

- $filter_备份管理类型 eq'Azure 存储'

由于所有必需的参数都位于 URI 中，因此无需单独的请求正文。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>响应

“刷新”操作是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它返回两个响应：创建另一个操作时为 202（已接受），当该操作完成时返回 200 （OK）。

##### <a name="example-responses"></a>示例响应

提交 POST 请求后，将返回 202（已接受）响应**。

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

使用带有简单 GET 命令的“Location”标头跟踪生成的操作**

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

发现所有 Azure 存储帐户后，GET 命令将返回 200（无内容）响应。 保管库现在能够发现任何具有可在订阅中备份的文件共享的存储帐户。

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>获取可以使用恢复服务保管库保护的存储帐户列表

要确认"缓存"已完成，请列出订阅下的所有可保护存储帐户。 然后在响应中找到所需的存储帐户。 这是使用 GET[可保护容器](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list)操作完成的。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI 包含所有必需的参数。 无需额外的请求正文。

响应正文示例：

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

由于我们可以在响应正文中找到具有友好名称的*testvault2*存储帐户，因此上述执行的刷新操作是成功的。 恢复服务保管库现在可以在同一订阅中成功发现具有未受保护的文件共享的存储帐户。

### <a name="register-storage-account-with-recovery-services-vault"></a>使用恢复服务保管库注册存储帐户

仅当之前未向保管库注册存储帐户时，才需要此步骤。 您可以通过[保护容器-注册操作](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)注册保管库。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

设置 URI 的变量，如下所示：

- [资源组名称] - *azure 文件*
- [结构名称] - *Azure*
- [保管库名称] - *azure 文件库*
- [容器名称] - 这是 GET 可保护容器操作的响应正文中的名称属性。
   在我们的示例中，它是*存储容器;储存;Azure 文件;测试库2*

>[!NOTE]
> 始终采用响应的名称属性并填写此请求。 不要硬编码或创建容器名称格式。 如果创建或硬编码，如果容器名称格式将来发生更改，API 调用将失败。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

创建请求正文如下所示：

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

有关请求正文定义和其他详细信息的完整列表，请参阅[保护容器注册](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer)。

这是异步操作，并返回两个响应："202 接受"操作被接受时，在操作完成后返回"200 OK"。  要跟踪操作状态，请使用位置标头获取操作的最新状态。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

操作完成后响应正文的示例：

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

可以从响应正文中的*注册状态*参数的值验证注册是否成功。 在我们的案例中，它显示了为*testvault2*注册的状态，因此注册操作成功。

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>查询存储帐户下的所有未受保护的文件共享

您可以使用[保护容器-询问](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire)操作查询存储帐户中的可保护项目。 这是异步操作，应使用位置标头跟踪结果。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

设置上述 URI 的变量，如下所示：

- [保管库名称] - *azure 文件库*
- [结构名称] - *Azure*
- [容器名称] - 请参阅 GET 可保护容器操作的响应正文中的名称属性。 在我们的示例中，它是*存储容器;储存;Azure 文件;测试库2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

请求成功后，它将返回状态代码"确定"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>选择要备份的文件共享

您可以在订阅下列出所有可保护的项目，并找到需要使用[GET 备份保护项目](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)操作进行备份的文件共享。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

构造 URI 如下所示：

- [保管库名称] - *azure 文件库*
- [$filter] -*备份管理类型 eq "Azure 存储"*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

示例响应：

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

响应包含所有未受保护的文件共享的列表，并包含 Azure 恢复服务配置备份所需的所有信息。

### <a name="enable-backup-for-the-file-share"></a>为文件共享启用备份

相关文件共享被"标识"后，使用友好名称，选择策略进行保护。 要了解有关保管库中现有策略的详细信息，请参阅[列表策略 API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)。 然后，通过引用策略名称来选择[相关策略](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get)。 若要创建策略，请参阅[创建策略教程](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)。

启用保护是创建"受保护项"的异步*PUT*操作。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

使用 GET 备份可保护项操作的响应正文中的 ID 属性设置**容器名称**和**受保护项名称**变量。

在我们的示例中，我们想要保护的文件共享的 ID 是：

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- [容器名称] -*存储容器;存储;azure文件;测试库2*
- [受保护的项目名称] - *azure 文件共享;测试共享*

或者，您可以引用保护容器和可保护项响应**的名称**属性。

>[!NOTE]
>始终采用响应的名称属性并填写此请求。 不要硬编码或创建容器名称格式或受保护项目名称格式。 如果创建或硬编码，如果容器名称格式或受保护项目名称格式将来发生更改，API 调用将失败。

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

创建请求正文：

以下请求正文定义创建受保护项所需的属性。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**源 ResourceId**是响应 GET 备份保护项目的**父容器 FabricID。**

示例响应

创建受保护项是异步操作，它创建需要跟踪的另一个操作。 它返回两个响应：创建另一个操作时为 202（已接受），当该操作完成时返回 200 （OK）。

提交保护项创建或更新*的 PUT*请求后，初始响应为 202（已接受），具有位置标头。

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

然后使用*GET*命令使用位置标头或 Azure-Async操作标头跟踪生成的操作。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

操作完成后，它将在响应正文中返回 200（确定），其中包含受保护的项内容。

示例响应正文：

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

这确认为文件共享启用了保护，并且第一个备份将根据策略计划触发。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>触发文件共享的按需备份

为备份配置 Azure 文件共享后，备份将按计划运行。 可以等待第一次计划备份或随时触发按需备份。

触发按需备份是一种 POST 操作。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

[容器名称] 和 [受保护项目名称] 在启用备份时构造为上面构造的。 对于我们的示例，这已转换为：

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>创建请求正文

对于触发按需备份，以下是请求正文的组成部分。

| “属性”       | 类型                       | 说明                       |
| ---------- | -------------------------- | --------------------------------- |
| 属性 | Azure 文件共享备份重新 | 备份请求资源属性 |

有关请求正文的完整定义列表和其他详细信息，请参阅[“触发受保护的项的备份”REST API 文档](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)。

请求正文示例

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>响应

触发按需备份是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它返回两个响应：创建另一个操作时为 202（已接受），当该操作完成时返回 200 （OK）。

### <a name="example-responses"></a>示例响应

一旦提交按需备份的 POST 请求后，初始响应为 202（已接受），其中包含 location 标头或 Azure-async 标头**。

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

然后使用*GET*命令使用位置标头或 Azure-Async操作标头跟踪生成的操作。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

操作完成后，它将返回 200（确定），其中包含响应正文中生成的备份作业的 ID。

#### <a name="sample-response-body"></a>示例响应正文

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

由于备份作业是长时间运行的操作，因此需要按[“使用 REST API 监视作业”文档](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job)中所述对其进行跟踪。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[Rest API 还原 Azure 文件共享](restore-azure-file-share-rest-api.md)。
