---
title: 使用 REST API 更新恢复服务保管库配置
description: 本文介绍如何使用 REST API 更新保管库配置。
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 3ee2d57b5589daa756020ebb787a5400ed244506
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890036"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>使用 REST API 更新 Azure 恢复服务保管库配置

本文介绍如何使用 REST API 更新 Azure 恢复服务保管库中与备份相关的配置。

## <a name="soft-delete-state"></a>软删除状态

删除受保护项的备份是一项必须加以监视的重要操作。 为防止意外删除，Azure 恢复服务保管库具有软删除功能。 此功能允许您在删除之后的某个时间段内还原已删除的备份。

但在某些情况下，不需要此功能。 如果 Azure 恢复服务保管库中有备份项（甚至软删除），则不能将其删除。 如果需要立即删除保管库，这可能会导致出现问题。 例如：部署操作通常会删除同一工作流中的已创建资源。 部署操作可以创建保管库，为项配置备份，执行测试还原，然后继续删除备份项和保管库。 如果保管库删除失败，则整个部署也可能会失败。 禁用软删除是确保立即删除的唯一方法。

因此，你需要根据具体方案谨慎选择是否对特定保管库禁用软删除。 有关详细信息，请参阅[软删除](backup-azure-security-feature-cloud.md)一文。

### <a name="fetch-soft-delete-state-using-rest-api"></a>使用 REST API 获取软删除状态

默认情况下，将为任何新建的恢复服务保管库启用软删除状态。 若要获取/更新保管库的软删除状态，请查看与备份保管库的配置相关的 [REST API 文档](/rest/api/backup/backupresourcevaultconfigs)

若要获取保管库的当前软删除状态，请使用以下 GET 操作

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI 包含参数 `{subscriptionId}`、`{vaultName}` 和 `{vaultresourceGroupName}`。 在本示例中，`{vaultName}` 是“testVault”，`{vaultresourceGroupName}` 是“testVaultRG”。 由于 URI 中给出了所有必需的参数，因此不需要单独的请求正文。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>响应

GET 操作的成功响应如下所示：

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>示例响应

提交 GET 请求后，将返回 200（成功）响应。

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

若要使用 REST API 更新恢复服务保管库的软删除状态，请使用以下 *修补* 操作

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

PATCH URI 包含参数 `{subscriptionId}`、`{vaultName}` 和 `{vaultresourceGroupName}`。 在本示例中，`{vaultName}` 是“testVault”，`{vaultresourceGroupName}` 是“testVaultRG”。 如果将 URI 替换为上述值，则 URI 将如下所示。

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>创建请求正文

以下通用定义用于创建请求正文

有关详细信息，请参阅 [REST API 文档](/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|名称  |必须  |类型  |说明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  可选的 eTag       |
|location     |  是       |String         |   资源位置      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  保管库的属性       |
|tags     |         | Object        |     资源标记    |

#### <a name="example-request-body"></a>示例请求正文

以下示例用于将软删除状态更新为“禁用”。

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>修补操作的响应

PATCH 操作的成功响应如下所示：

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>修补操作的示例响应

提交 PATCH 请求后，将返回 200 （成功）响应。

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
