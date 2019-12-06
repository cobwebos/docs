---
title: 管理 Azure 存储生命周期
description: 了解如何创建生命周期策略规则，以将陈旧数据从热存储转移到冷存储和存档层。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: f5578d00d633b4b1ccce41236526e1696744f59f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851768"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>管理 Azure Blob 存储生命周期

数据集具有独特的生命周期。 在生命周期的早期，人们经常访问某些数据。 但随着数据的老化，访问需求急剧下降。 有些数据在云中保持空闲状态，并且在存储后很少被访问。 有些数据在创建后的数日或者数月即会过期，还有一些数据集在其整个生存期会频繁受到读取和修改。 Azure Blob 存储生命周期管理为 GPv2 和 Blob 存储帐户提供了丰富的基于规则的策略。 可使用该策略将数据转移到适当的访问层，或在数据的生命周期结束时使数据过期。

生命周期管理策略允许：

- 将 Blob 转移到较冷的存储层（从热到冷、从热到存档，或者从冷到存档），以便针对性能和成本进行优化
- 删除生命周期已结束的 Blob
- 在存储帐户级别定义每天运行一次的规则
- 将规则应用到容器或 Blob 子集（使用前缀作为筛选器）

请考虑这样一种情况：数据在生命周期的早期阶段获得频繁的访问，但在两周内偶尔发生。 一个月以后，该数据集很少被访问。 在这种场景下，早期阶段最适合使用热存储。 冷存储最适用于偶尔访问。 将数据保存在一个月后，存档存储是最佳的 "层" 选项。 通过根据数据陈旧程度调整存储层，可根据需求设计出最具性价比的存储选项。 若要实现这种过渡，可以使用生命周期管理策略规则将陈旧数据转移到较冷的存储层。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>存储帐户支持

生命周期管理策略可用于常规用途 v2 （GPv2）帐户、Blob 存储帐户和高级块 Blob 存储帐户。 在 Azure 门户中，你可以将现有常规用途（GPv1）帐户升级为 GPv2 帐户。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。  

## <a name="pricing"></a>价格

生命周期管理功能免费。 客户需要支付[列出 Blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 和[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 调用的常规操作费用。 删除操作免费。 有关定价的详细信息，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability"></a>区域可用性

生命周期管理功能在所有 Azure 区域中均可用。

## <a name="add-or-remove-a-policy"></a>添加或删除策略

您可以使用以下任一方法来添加、编辑或删除策略：

* [Azure 门户](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

可以完整读取或写入策略。 不支持部分更新。 

> [!NOTE]
> 如果为存储帐户启用了防火墙规则，生命周期管理请求可能会被阻止。 可以通过为受信任的 Microsoft 服务提供例外，来取消阻止这些请求。 有关详细信息，请参阅[配置防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)中的“例外”部分。

本文介绍如何使用门户和 PowerShell 方法管理策略。  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

可以通过两种方法通过 Azure 门户添加策略。 

* [Azure 门户列表视图](#azure-portal-list-view)
* [Azure 门户代码视图](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure 门户列表视图

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择 "**所有资源**"，然后选择存储帐户。

3. 在 " **Blob 服务**" 下，选择 "**生命周期管理**" 以查看或更改你的规则。

4. 选择 "**列表视图**" 选项卡。

5. 选择 "**添加规则**"，然后填写**操作设置**窗体字段。 在以下示例中，如果 blob 未修改30天，则将其移动到冷存储。

   ![Azure 门户中的生命周期管理操作集页](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. 选择 "**筛选器集**" 添加可选筛选器。 然后，选择 "**浏览**" 以指定要筛选的容器和文件夹。

   ![Azure 门户中的生命周期管理筛选器集页](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. 选择 "查看" " **+ 添加**" 以查看策略设置。

9. 选择 "**添加**" 以添加新策略。

#### <a name="azure-portal-code-view"></a>Azure 门户代码视图
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择 "**所有资源**"，然后选择存储帐户。

3. 在 " **Blob 服务**" 下，选择 "**生命周期管理**" 以查看或更改策略。

4. 以下 JSON 是可粘贴到 "**代码视图**" 选项卡中的策略的示例。

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. 选择“保存”。

6. 有关此 JSON 示例的详细信息，请参阅 "[策略](#policy)" 和 "[规则](#rules)" 部分。

# <a name="powershelltabazure-powershell"></a>[Powershell](#tab/azure-powershell)

以下 PowerShell 脚本可用于将策略添加到存储帐户。 必须用资源组名称初始化 `$rgname` 变量。 必须用您的存储帐户名称初始化 `$accountName` 变量。

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="templatetabtemplate"></a>[模板](#tab/template)

可以使用 Azure 资源管理器模板来定义生命周期管理。 下面是一个示例模板，用于部署 GRS GPv2 存储帐户和生命周期管理策略。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>策略

生命周期管理策略是 JSON 文档中的规则集合：

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

策略是规则的集合：

| 参数名称 | 参数类型 | 说明 |
|----------------|----------------|-------|
| `rules`        | 规则对象的数组 | 策略中至少需要一个规则。 最多可在一个策略中定义100个规则。|

策略内的每个规则都有多个参数：

| 参数名称 | 参数类型 | 说明 | 需要 |
|----------------|----------------|-------|----------|
| `name`         | 字符串 |规则名称最多可以包含256个字母数字字符。 规则名称区分大小写。  该名称必须在策略中唯一。 | 正确 |
| `enabled`      | 布尔 | 一个可选的布尔值，允许暂时禁用规则。 如果未设置，则默认值为 true。 | 错误 | 
| `type`         | 枚举值 | 当前有效类型为 `Lifecycle`。 | 正确 |
| `definition`   | 定义生命周期规则的对象 | 每个定义均由筛选器集和操作集组成。 | 正确 |

## <a name="rules"></a>规则

每个规则定义包括筛选器集和操作集。 [筛选器集](#rule-filters)将规则操作限制为容器或对象名称中的某组对象。 [操作集](#rule-actions)对筛选的对象集应用分层或删除操作。

### <a name="sample-rule"></a>示例规则

下面的示例规则筛选帐户，以便对 `container1` 中存在的对象执行操作，并以 `foo`开头。  

- 在上次修改后的 30 天后，将 Blob 分层到冷层
- 在上次修改后的 90 天后，将 Blob 分层到存档层
- 在上次修改后的 2,555 天（7 年）后，删除 Blob
- 在创建快照后的 90 天后，删除 Blob 快照

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>规则筛选器

筛选器将规则操作限制为存储帐户中的 Blob子集。 如果定义了多个筛选器，将对所有筛选器运行逻辑 `AND`。

筛选器包括：

| 筛选器名称 | 筛选器类型 | 说明 | 是否必需 |
|-------------|-------------|-------|-------------|
| blobTypes   | 预定义枚举值的数组。 | 当前版本支持 `blockBlob`。 | 是 |
| prefixMatch | 要匹配的前缀字符串数组。 每个规则最多可定义10个前缀。 前缀字符串必须以容器名称开头。 例如，如果要匹配规则 `https://myaccount.blob.core.windows.net/container1/foo/...` 下的所有 blob，则 `container1/foo`prefixMatch。 | 如果未定义 prefixMatch，则规则将应用于存储帐户中的所有 blob。  | No |

### <a name="rule-actions"></a>规则操作

当符合运行条件时，操作将应用于筛选的 blob。

生命周期管理支持对 blob 和删除 blob 快照进行分层和删除。 在 Blob 或 Blob 快照中为每个规则至少定义一个操作。

| 行动        | 基本 Blob                                   | 快照      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 目前支持位于热层的 Blob         | 不支持 |
| tierToArchive | 目前支持位于热层或冷层的 Blob | 不支持 |
| delete        | 受支持                                   | 受支持     |

>[!NOTE]
>如果在同一 Blob 中定义了多个操作，生命周期管理将对该 Blob 应用开销最低的操作。 例如，操作 `delete` 的开销比 `tierToArchive` 更低。 操作 `tierToArchive` 的开销比 `tierToCool` 更低。

运行条件基于年龄。 基本 Blob 使用上次修改时间来跟踪陈旧程度，Blob 快照使用快照创建时间来跟踪陈旧程度。

| 操作运行条件             | 条件值                          | 描述                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 指示陈旧程度（天）的整数值 | 基本 blob 操作的条件     |
| daysAfterCreationGreaterThan     | 指示陈旧程度（天）的整数值 | Blob 快照操作的条件 |

## <a name="examples"></a>示例

以下示例演示如何使用生命周期策略规则满足常见场景。

### <a name="move-aging-data-to-a-cooler-tier"></a>将陈旧的数据移到冷层

此示例演示如何转移前缀为 `container1/foo` 或 `container2/bar` 的块 Blob。 该策略将 30 天以上未修改的 Blob 转移到冷存储，并将 90 天未修改的 Blob 转移到存档层：

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>引入时存档数据

某些数据在云中保持空闲状态，并且在存储后很少（如果有）被访问。 以下生命周期策略配置为在引入数据后对其进行存档。 此示例将容器 `archivecontainer` 的存储帐户中的块 blob 转换为存档层。 通过在上次修改时间之后的 blob 0 天执行转换：

> [!NOTE] 
> 建议将 blob 直接上传到 "存档" 层以提高效率。 可以将[PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob)或[PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list)的 x-ms 跨区标头用于 REST 版本2018-11-09 和更高版本或最新的 blob 存储客户端库。 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>基于陈旧程度使数据过期

某些数据应在创建后的几天或几个月后过期。 可以将生命周期管理策略配置为：根据数据陈旧程度删除数据，以使数据过期。 下面的示例演示了一个策略，该策略删除所有早于365天的块 blob。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>删除旧快照

对于在整个生存期内频繁修改和访问的数据，通常会使用快照来跟踪数据的旧版本。 可以创建一个策略，用于根据快照的陈旧程度删除旧快照。 可通过评估快照创建时间来确定快照的陈旧程度。 此策略规则删除容器 `activedata` 中自创建快照后达到或超过 90 天的块 Blob 快照。

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>常见问题解答

**我创建了一个新策略，为什么这些操作不会立即运行？**  
平台每天运行一次生命周期策略。 配置策略后，第一次运行某些操作可能需要长达24小时。  

**如果更新现有策略，运行操作需要多长时间？**  
更新后的策略最多需要24小时才能生效。 策略生效后，运行操作可能需要长达24小时。 因此，此策略可能需要长达48小时才能执行。   

**我手动解除冻结存档的 blob，如何防止它暂时移回存档层？**  
将 blob 从一个访问层移到另一个访问层时，其上次修改时间将不会更改。 如果将存档的 blob 手动解除冻结到 "热" 层，则生命周期管理引擎会将该 blob 移回存档层。 禁用影响此 blob 的规则，以防止它再次存档。 当 blob 可以安全地移回存档层时，重新启用规则。 还可以将 blob 复制到其他位置，前提是该 blob 需要永久保留在 "热" 或 "冷" 层中。

## <a name="next-steps"></a>后续步骤

了解如何在意外删除数据后恢复数据：

- [Azure 存储 Blob 的软删除](../blobs/storage-blob-soft-delete.md)
