---
title: 使用 REST API 备份 Azure Vm
description: 本文介绍如何使用 REST API 配置、启动和管理 Azure VM 备份的备份操作。
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395547"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>通过 REST API 使用 Azure 备份来备份 Azure VM

本文介绍如何通过 REST API 使用 Azure API 来管理 Azure VM 的备份。 第一次为之前未受保护的 Azure VM 配置保护，为受保护的 Azure VM 触发按需备份，并通过 REST API 修改已备份 VM 的备份属性，如此处所述。

有关创建新保管库和策略的信息，请参阅[创建保管库](backup-azure-arm-userestapi-createorupdatevault.md)和[创建策略](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API 教程。

假设你想将资源组“testRG”下的 VM“testVM”放置到恢复服务保管库“testVault”中进行保护（该保管库在资源组“testVaultRG”中，并且使用名为“DefaultPolicy”的默认策略）。

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>使用 REST API 为未受保护的 Azure VM 配置备份

### <a name="discover-unprotected-azure-vms"></a>发现未受保护的 Azure VM

首先，保管库应能够识别 Azure VM。 这是使用[刷新操作](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)触发的。 这是一种异步 POST 操作，可确保保险库获取当前订阅中所有未受保护的 VM 的最新列表并“缓存”它们。 一旦 VM 完成缓存，恢复服务将能够访问 VM，并对其进行保护。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST URI 包含参数 `{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}`、`{fabricName}`。 `{fabricName}` 是“Azure”。 根据示例，`{vaultName}` 是“testVault”，`{vaultresourceGroupName}` 是“testVaultRG”。 由于 URI 中给出了所有必需的参数，因此不需要单独的请求正文。

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Responses

“刷新”操作是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |Type  |说明  |
|---------|---------|---------|
|204 无内容     |         |  确定无内容返回      |
|202 已接受     |         |     已接受    |

##### <a name="example-responses"></a>示例响应

提交 POST 请求后，将返回 202（已接受）响应。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

使用带有简单 GET 命令的“Location”标头跟踪生成的操作

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

一旦发现所有 Azure VM 后，GET 命令将返回 204（无内容）响应。 保管库现在能够发现订阅中的任何 VM。

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>选择相关的 Azure VM

 可以通过在订阅下[列出所有可保护项](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)来确认“缓存”已完成，并在响应中找到所需的 VM。 [此操作的响应](#example-responses-1)还提供有关恢复服务如何识别 VM 的信息。  熟悉此模式后，可以跳过此步骤直接进入[启用保护](#enabling-protection-for-the-azure-vm)。

此操作是一种 GET 操作。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*GET* URI 包含所有必需的参数。 无需额外的请求正文。

#### <a name="responses-1"></a>响应

|名称  |Type  |说明  |
|---------|---------|---------|
|200 正常     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-1"></a>示例响应

提交 GET 请求后，将返回 200（确定）响应。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> GET 响应中每“页”的值数量限制为 200。 使用“nextLink”字段获取下一组响应的 URL。

响应包含所有未受保护的 Azure VM 的列表，并且每个 `{value}` 包含 Azure 恢复服务配置备份所需的所有信息。 若要配置备份，请注意 `{properties}` 节中的 `{name}` 字段和 `{virtualMachineId}` 字段。 按下方所示，通过这些字段值构造两个变量。

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- 稍后将在[请求正文](#example-request-body)中使用 `{virtualMachineId}`

在此示例中，上述值转换为：

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>为 Azure VM 启用保护

在“缓存”和“识别”相关 VM 后，选择要保护的策略。 若要了解有关保管库中现有策略的更多信息，请参阅[列出策略 API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)。 然后，通过引用策略名称来选择[相关策略](/rest/api/backup/protectionpolicies/get)。 若要创建策略，请参阅[创建策略教程](backup-azure-arm-userestapi-createorupdatepolicy.md)。 下面的示例中选择了“DefaultPolicy”。

启用保护是一种异步 PUT 操作，可创建“受保护的项”。

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 是按上述方法构造的。 `{fabricName}` 是“Azure”。 对于我们的示例，这已转换为：

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>创建请求正文

对于创建受保护的项，以下是请求正文的组成部分。

|名称  |Type  |说明  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem 资源属性         |

有关请求正文的完整定义列表和其他详细信息，请参阅[“创建受保护的项”REST API 文档](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body)。

##### <a name="example-request-body"></a>示例请求正文

以下请求正文定义创建受保护项所需的属性。

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` 是上面提到的[列出可保护项的响应](#example-responses-1)中的 `{virtualMachineId}`。

#### <a name="responses"></a>Responses

创建受保护项的是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |Type  |说明  |
|---------|---------|---------|
|200 正常     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 已接受     |         |     已接受    |

##### <a name="example-responses"></a>示例响应

一旦提交“PUT”请求以创建或更新受保护的项后，初始响应为 202（已接受），其中包含 location 标头或 Azure-async 标头。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然后通过简单的 GET 命令并使用 location 标头或 Azure-AsyncOperation 标头跟踪生成的操作。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

操作完成后，它将在响应正文中返回 200（确定），其中包含受保护的项内容。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

这可确认已为 VM 启用了保护，并且将根据策略计划触发第一次备份。

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>为受保护的 Azure VM 触发按需备份

配置 Azure VM 以进行备份后，将根据策略计划执行备份。 可以等待第一次计划备份或随时触发按需备份。 按需备份的保留期与备份策略的保留期是不同的，并且可以指定为特定的日期时间。 如果未指定，则假定为触发按需备份之日后的 30 天。

触发按需备份是一种 POST 操作。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` 和 `{protectedItemName}` 是按[上述方法](#responses-1)构造的。 `{fabricName}` 是“Azure”。 对于我们的示例，这已转换为：

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>创建请求正文

对于触发按需备份，以下是请求正文的组成部分。

|名称  |Type  |说明  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource 属性         |

有关请求正文的完整定义列表和其他详细信息，请参阅[“触发受保护的项的备份”REST API 文档](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)。

#### <a name="example-request-body"></a>示例请求正文

以下请求正文定义了触发受保护项的备份所需的属性。 如果未指定保留期，则保留期将为触发备份作业开始后的 30 天。

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Responses

触发按需备份是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |Type  |说明  |
|---------|---------|---------|
|202 已接受     |         |     已接受    |

#### <a name="example-responses-3"></a>示例响应

一旦提交按需备份的 POST 请求后，初始响应为 202（已接受），其中包含 location 标头或 Azure-async 标头。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然后通过简单的 GET 命令并使用 location 标头或 Azure-AsyncOperation 标头跟踪生成的操作。

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

操作完成后，它将返回 200（确定），其中包含响应正文中生成的备份作业的 ID。

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

由于备份作业是长时间运行的操作，因此需要按[“使用 REST API 监视作业”文档](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)中所述对其进行跟踪。

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>修改受保护的 Azure VM 的备份配置

### <a name="changing-the-policy-of-protection"></a>更改保护策略

若更改受保护 VM 的策略，可以使用与[启用保护](#enabling-protection-for-the-azure-vm)相同的格式。 只需在[请求正文](#example-request-body)中提供新的策略 ID 并提交请求即可。 例如：若要将 testVM 的策略从 "DefaultPolicy" 更改为 "ProdPolicy"，请在请求正文中提供 "ProdPolicy" ID。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

响应将与上文所述的[启用保护](#responses-2)中采用相同的格式

### <a name="stop-protection-but-retain-existing-data"></a>停止保护，但保留现有数据

若要删除对受保护的 VM 的保护但保留已备份的数据，请删除请求正文中的策略并提交请求。 删除与策略的关联后，将不再触发备份，也不会创建新的恢复点。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

响应将与上文所述的[触发按需备份](#example-responses-3)中采用相同的格式。 应按[“使用 REST API 监视作业”文档](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)中所述来跟踪生成的作业。

### <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

若要删除对受保护的 VM 的保护并删除备份数据，请执行[此处](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)详细的删除操作。

停止保护和删除数据是一种 DELETE 操作。

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 是按[上述方法](#responses-1)构造的。 `{fabricName}` 是“Azure”。 对于我们的示例，这已转换为：

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-2"></a>响应

DELETE 操作是一种[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：202（接受）：创建另一个操作，然后在该操作完成时执行204（NoContent）。

|名称  |Type  |说明  |
|---------|---------|---------|
|204 无内容     |         |  无内容       |
|202 已接受     |         |     已接受    |

> [!IMPORTANT]
> 为了防止意外删除方案，有一个可用于恢复服务保管库的[软删除功能](use-restapi-update-vault-properties.md#soft-delete-state)。 如果保管库的软删除状态设置为 "已启用"，则删除操作不会立即删除数据。 它将保留14天，然后被永久清除。 此14天内不会向客户收取存储费用。 若要撤消删除操作，请参阅[撤消-删除部分](#undo-the-stop-protection-and-delete-data)。

### <a name="undo-the-stop-protection-and-delete-data"></a>撤消停止保护并删除数据

撤消意外删除类似于创建备份项。 撤消删除后，将保留该项，但不会触发任何将来的备份。

撤消删除操作是一个非常类似于[更改策略](#changing-the-policy-of-protection)和/或[启用保护的](#enabling-protection-for-the-azure-vm) *PUT*操作。 只需通过[请求正文](#example-request-body)中的变量*isRehydrate*来撤消删除操作，并提交请求。 例如：若要撤消对 testVM 的删除操作，应使用以下请求正文。

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

响应将与上文所述的[触发按需备份](#example-responses-3)中采用相同的格式。 应按[“使用 REST API 监视作业”文档](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)中所述来跟踪生成的作业。

## <a name="next-steps"></a>后续步骤

[从 Azure 虚拟机备份还原数据](backup-azure-arm-userestapi-restoreazurevms.md)。

有关 Azure 备份 REST API 的详细信息，请参阅以下文档：

- [Azure 恢复服务提供程序 REST API](/rest/api/recoveryservices/)
- [Azure REST API 入门](/rest/api/azure/)
