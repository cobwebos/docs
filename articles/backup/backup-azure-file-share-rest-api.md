---
title: 与 REST API 备份 Azure 文件共享
description: 了解如何使用 REST API 在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393176"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>通过 Rest API 使用 Azure 备份来备份 Azure 文件共享

本文介绍如何通过 REST API 使用 Azure 备份来备份 Azure 文件共享。

本文假设已创建了恢复服务保管库以及用于为文件共享配置备份的策略。 如果尚未创建，请参阅[创建保管库](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault)和[创建策略](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)REST API 教程，以创建新的保管库和策略。

对于本文，我们将使用以下资源：

- **RecoveryServicesVault**： *azurefilesvault*

- **策略：** *schedule1*

- **资源组**： *azurefiles*

- **存储帐户**： *testvault2*

- **文件共享**： *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>使用 REST API 为未受保护的 Azure 文件共享配置备份

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>发现具有未受保护的 Azure 文件共享的存储帐户

保管库需要通过可备份到恢复服务保管库的文件共享来发现订阅中的所有 Azure 存储帐户。 这是使用[刷新操作](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)触发的。 这是一种异步*POST*操作，可确保保管库获取当前订阅中所有未受保护的 Azure 文件共享的最新列表，并将其 "缓存"。 文件共享 "已缓存" 后，恢复服务可以访问文件共享并对其进行保护。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI 有 `{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}`和 `{fabricName}` 参数。 在我们的示例中，不同参数的值如下所示：

- `{fabricName}` 是*Azure*

- `{vaultName}` *azurefilesvault*

- `{vaultresourceGroupName}` *azurefiles*

- $filter = backupManagementType eq ' AzureStorage '

由于在 URI 中提供了所有必需的参数，因此无需单独的请求正文。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>响应

“刷新”操作是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它在创建另一操作时返回两个响应：202（接受），而在该操作完成时返回200（OK）。

##### <a name="example-responses"></a>示例响应

提交 POST 请求后，将返回 202（已接受）响应。

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

使用带有简单 GET 命令的“Location”标头跟踪生成的操作

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

发现所有 Azure 存储帐户后，GET 命令将返回200（无内容）响应。 保管库现在能够发现任何具有可在订阅中备份的文件共享的存储帐户。

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

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>获取可通过恢复服务保管库保护的存储帐户列表

若要确认已完成 "缓存"，请列出订阅下的所有可保护的存储帐户。 然后在响应中找到所需的存储帐户。 使用[GET ProtectableContainers](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list)操作即可完成此操作。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI 包含所有必需的参数。 无需额外的请求正文。

响应正文的示例：

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

由于我们可以在响应正文中找到具有友好名称的*testvault2*存储帐户，因此已成功执行上述刷新操作。 恢复服务保管库现在可以成功发现同一订阅中具有不受保护的文件共享的存储帐户。

### <a name="register-storage-account-with-recovery-services-vault"></a>向恢复服务保管库注册存储帐户

仅当之前未向保管库注册存储帐户时，才需要执行此步骤。 可以通过[ProtectionContainers 注册操作](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register)来注册保管库。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

按如下所示设置 URI 的变量：

- {resourceGroupName}- *azurefiles*
- {fabricName}- *Azure*
- {vaultName}- *azurefilesvault*
- {容器名称}-这是 GET ProtectableContainers 操作的响应正文中的 name 属性。
   在我们的示例中，它是*StorageContainer 的;储存AzureFiles; testvault2*

>[!NOTE]
> 始终采用响应的 name 属性，并填写此请求。 不要硬编码或创建容器名称格式。 如果创建或硬编码此容器，则在将来的容器名称格式发生变化时，API 调用将失败。

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

有关请求正文定义和其他详细信息的完整列表，请参阅[ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer)。

这是一个异步操作，并返回两个响应： "202 接受" （当操作被接受时）和 "200 OK" （当操作完成时）。  若要跟踪操作状态，请使用 location 标头获取操作的最新状态。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

操作完成时响应正文的示例：

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

可以验证注册是否成功，从响应正文的*registrationstatus*参数的值。 在我们的示例中，它显示*testvault2*注册的状态，因此注册操作成功。

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>在存储帐户下查询所有不受保护的文件共享

您可以使用[保护容器-查询](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire)操作来查询存储帐户中的可保护项。 这是一个异步操作，应使用 location 标头跟踪结果。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

按如下所示设置上述 URI 的变量：

- {vaultName}- *azurefilesvault*
- {fabricName}- *Azure*
- {容器名称}-请参阅 GET ProtectableContainers 操作的响应正文中的 name 属性。 在我们的示例中，它是*StorageContainer 的;储存AzureFiles; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

请求成功后，将返回状态代码 "确定"

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

可以列出订阅下的所有可保护项，并使用[GET backupprotectableItems](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)操作找到要备份的所需文件共享。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

按如下所示构造 URI：

- {vaultName}- *azurefilesvault*
- {$filter}- *backupManagementType eq ' AzureStorage '*

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

在相关文件共享具有友好名称后，请选择要保护的策略。 若要详细了解保管库中的现有策略，请参阅[列表策略 API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)。 然后，通过引用策略名称来选择[相关策略](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get)。 若要创建策略，请参阅[创建策略教程](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy)。

启用保护是一项异步*PUT*操作，用于创建 "受保护的项"。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

使用 GET backupprotectableitems 操作的响应正文中的 ID 属性设置**容器**和**protecteditemname**变量。

在我们的示例中，要保护的文件共享的 ID 是：

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- { *storagecontainer; storage; azurefiles; testvault2*
- {protectedItemName}- *azurefileshare; testshare*

或者，可以引用保护容器的 "**名称**" 属性和 "可保护的项" 响应。

>[!NOTE]
>始终采用响应的 name 属性，并填写此请求。 不要硬编码或创建容器名称格式或受保护的项名称格式。 如果创建或硬编码，则 API 调用将失败，前提是容器名称格式或受保护的项名称格式在将来发生更改。

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

**SourceResourceId**是**ParentcontainerFabricID**以获取 backupprotectableItems 的响应。

示例响应

创建受保护项是一个异步操作，该操作将创建需要跟踪的其他操作。 当创建另一个操作时，它将返回两个响应：202（已接受），并在该操作完成时返回200（确定）。

提交用于受保护项创建或更新的*PUT*请求后，初始响应为202（接受）和 location 标头。

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

然后使用 location 标头或 AsyncOperation 标头通过*GET*命令跟踪生成的操作。

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

这会确认为文件共享启用了保护，并根据策略计划触发首个备份。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>触发文件共享的按需备份

将 Azure 文件共享配置为进行备份后，将根据策略计划运行备份。 可以等待第一次计划备份或随时触发按需备份。

触发按需备份是一项 POST 操作。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

启用备份时，{容器} 和 {protectedItemName} 按上述方式构造。 对于我们的示例，这已转换为：

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>创建请求正文

对于触发按需备份，以下是请求正文的组成部分。

| 名称       | 类型                       | 说明                       |
| ---------- | -------------------------- | --------------------------------- |
| 属性 | AzurefilesharebackupReques | BackupRequestResource 属性 |

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

当创建另一个操作时，它将返回两个响应：202（已接受），并在该操作完成时返回200（确定）。

### <a name="example-responses"></a>示例响应

一旦提交按需备份的 POST 请求后，初始响应为 202（已接受），其中包含 location 标头或 Azure-async 标头。

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

然后使用 location 标头或 AsyncOperation 标头通过*GET*命令跟踪生成的操作。

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

- 了解如何[使用 REST API 还原 Azure 文件共享](restore-azure-file-share-rest-api.md)。
