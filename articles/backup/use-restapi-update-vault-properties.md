---
title: 用 REST API 更新恢复服务保管库配置
description: 本文介绍如何使用 REST API 更新保管库的配置。
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395548"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>使用 REST API 更新 Azure 恢复服务保管库配置

本文介绍如何使用 REST API 更新 Azure 恢复服务保管库中的备份相关配置。

## <a name="soft-delete-state"></a>软删除状态

删除受保护项的备份是一项重要的操作，必须进行监视。 为了防止意外删除，Azure 恢复服务保管库具有软删除功能。 此功能允许客户在删除后的某个时间段内还原已删除的备份。

但在某些情况下，不需要此功能。 如果 Azure 恢复服务保管库中有备份项（甚至软删除），则无法将其删除。 如果需要立即删除保管库，这可能会导致问题。 例如：部署操作通常会在同一工作流中清理已创建的资源。 部署可以创建保管库，为项配置备份，执行测试还原，然后继续删除备份项和保管库。 如果保管库删除失败，则整个部署可能会失败。 禁用软删除是保证立即删除的唯一方法。

因此，客户需要仔细选择是否禁用特定保管库的软删除，具体取决于方案。 有关详细信息，请参阅[软删除文章](backup-azure-security-feature-cloud.md#soft-delete)。

### <a name="fetch-soft-delete-state-using-rest-api"></a>使用 REST API 提取软删除状态

默认情况下，将为任何新创建的恢复服务保管库启用软删除状态。 若要获取/更新保管库的软删除的状态，请使用备份保管库的配置相关[REST API 文档](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

若要获取保管库的软删除的当前状态，请使用以下*GET*操作

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI 有 `{subscriptionId}`、`{vaultName}``{vaultresourceGroupName}` 参数。 在此示例中，`{vaultName}` 是 "testVault"，`{vaultresourceGroupName}` 为 "testVaultRG"。 由于 URI 中给出了所有必需的参数，因此不需要单独的请求正文。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>响应

"GET" 操作的成功响应如下所示：

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>示例响应

提交 "GET" 请求后，将返回200（成功）响应。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>使用 REST API 更新软删除状态

若要使用 REST API 更新恢复服务保管库的软删除状态，请使用以下*修补*操作

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

修补程序 URI 有 `{subscriptionId}`、`{vaultName}``{vaultresourceGroupName}` 参数。 在此示例中，`{vaultName}` 是 "testVault"，`{vaultresourceGroupName}` 为 "testVaultRG"。 如果将 URI 替换为上述值，则 URI 将如下所示。

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>创建请求正文

以下公共定义用于创建请求正文

有关更多详细信息，请参阅[REST API 文档](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|名称  |必选  |类型  |说明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  可选的 eTag       |
|location     |  true       |String         |   资源位置      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  保管库的属性       |
|标记     |         | Object        |     资源标记    |

#### <a name="example-request-body"></a>示例请求正文

下面的示例用于将软删除状态更新为 "disabled"。

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>响应

"修补程序" 操作的成功响应如下所示：

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>示例响应

提交 "PATCH" 请求后，将返回200（成功）响应。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>后续步骤

[创建用于在此保管库中备份 Azure VM 的备份策略](backup-azure-arm-userestapi-createorupdatepolicy.md)。

有关 Azure REST API 的详细信息，请参阅以下文档：

- [Azure 恢复服务提供程序 REST API](/rest/api/recoveryservices/)
- [Azure REST API 入门](/rest/api/azure/)
