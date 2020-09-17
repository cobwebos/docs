---
title: 管理 Azure 存储生命周期
description: 了解如何创建生命周期策略规则，以将陈旧数据从热存储转移到冷存储和存档层。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/15/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: d47b9b5882b25ee030ca813abbaf77805b2df0f5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707758"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>管理 Azure Blob 存储生命周期

数据集具有独特的生命周期。 在生命周期的早期，人们经常访问某些数据。 但随着数据的老化，访问需求急剧下降。 有些数据在云中保持空闲状态，并且在存储后很少被访问。 有些数据在创建后的数日或者数月即会过期，还有一些数据集在其整个生存期会频繁受到读取和修改。 Azure Blob 存储生命周期管理为 GPv2 和 Blob 存储帐户提供丰富的基于规则的策略。 可使用该策略将数据转移到适当的访问层，或在数据的生命周期结束时使数据过期。

生命周期管理策略允许：

- 将 Blob 转移到较冷的存储层（从热到冷、从热到存档，或者从冷到存档），以便针对性能和成本进行优化
- 删除生命周期已结束的 Blob
- 在存储帐户级别定义每天运行一次的规则
- 使用名称前缀或 [blob 索引标记](storage-manage-find-blobs.md) 作为筛选器，将规则应用于容器或部分 blob () 

假设某个数据集在生命周期的早期阶段频繁被访问，两周后只是偶尔被访问。 一个月以后，该数据集很少被访问。 在这种场景下，早期阶段最适合使用热存储。 在偶尔访问阶段最适合使用冷存储。 在一个月后数据陈旧时，存档存储便是最佳的层选项。 通过根据数据陈旧程度调整存储层，可根据需求设计出最具性价比的存储选项。 若要实现这种过渡，可以使用生命周期管理策略规则将陈旧数据转移到较冷的存储层。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>可用性和定价

生命周期管理功能在所有 Azure 区域中适用于常规用途 v2 (GPv2) 帐户、Blob 存储帐户和高级块 Blob 存储帐户。 在 Azure 门户中，可将现有的常规用途 (GPv1) 帐户升级为 GPv2 帐户。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

生命周期管理功能是免费的。 客户需要支付[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 调用的常规操作费用。 删除操作是免费的。 有关定价的详细信息，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="add-or-remove-a-policy"></a>添加或删除策略

可以使用以下任一方法来添加、编辑或删除策略：

* [Azure 门户](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

可以完整读取或写入策略。 不支持部分更新。 

> [!NOTE]
> 如果为存储帐户启用了防火墙规则，生命周期管理请求可能会被阻止。 可以通过为受信任的 Microsoft 服务提供例外，来取消阻止这些请求。 有关详细信息，请参阅[配置防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)中的“例外”部分。

本文介绍如何使用门户和 PowerShell 方法管理策略。

# <a name="portal"></a>[Portal](#tab/azure-portal)

可以在 Azure 门户中通过两种方式添加策略。 

* [Azure 门户列表视图](#azure-portal-list-view)
* [Azure 门户代码视图](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure 门户列表视图

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择你的存储帐户。 

1. 在 " **Blob 服务**" 下，选择 " **生命周期管理** " 以查看或更改你的规则。

1. 选择 " **列表视图** " 选项卡。

1. 选择 " **添加规则** "，并在 **详细信息** 窗体上命名规则。 还可以设置 **规则范围**、 **Blob 类型**和 **blob 子类型** 值。 下面的示例将范围设置为筛选 blob。 这将导致添加 " **筛选器集** " 选项卡。

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="生命周期管理在 Azure 门户中添加规则详细信息页":::

1. 选择 " **基本 blob** " 以设置规则的条件。 在以下示例中，如果 Blob 有 30 天未修改，它们将转移到冷存储。

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Azure 门户中的生命周期管理基础 blob 页":::

   " **上次访问** 时间" 选项在以下区域的预览中提供：

    - 法国中部
    - 加拿大东部
    - 加拿大中部

   > [!IMPORTANT]
   > 上次访问时间跟踪预览版仅适用于非生产。 生产服务级别协议 (SLA) 当前不可用。
   
   若要使用 "**上次访问**时间" 选项，请在 "Azure 门户的"**生命周期管理**"页上选择"**访问跟踪已启用**"。 有关 **最后访问** 的选项的详细信息，请参阅 [基于上次访问日期移动数据 (预览) ](#move-data-based-on-last-accessed-date-preview)。

1. 如果在 "**详细信息**" 页上选择了 "**使用筛选器限制 blob** "，请选择 "**筛选器集**" 添加可选筛选器。 下面的示例筛选 *mylifecyclecontainer* 容器中以 "log" 开头的 blob。

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Azure 门户中的生命周期管理筛选器集页":::

1. 选择“添加”以添加新策略。

#### <a name="azure-portal-code-view"></a>Azure 门户代码视图
1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择你的存储帐户。

1. 在 " **Blob 服务**" 下，选择 " **生命周期管理** " 以查看或更改策略。

1. 以下 JSON 是可粘贴到 " **代码视图** " 选项卡中的策略的示例。

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. 选择“保存” 。

1. 有关此 JSON 示例的详细信息，请参阅[策略](#policy)和[规则](#rules)部分。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell 脚本可将策略添加到存储帐户。 必须使用资源组名称初始化 `$rgname` 变量。 必须使用存储帐户名称初始化 `$accountName` 变量。

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

# <a name="template"></a>[模板](#tab/template)

可以使用 Azure 资源管理器模板定义生命周期管理。 以下示例模板可以使用生命周期管理策略部署 RA-GRS GPv2 存储帐户。

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

| 参数名称 | 参数类型 | 注释 |
|----------------|----------------|-------|
| `rules`        | 规则对象的数组 | 一个策略至少需要包含一个规则。 最多可在一个策略中定义 100 个规则。|

策略中的每个规则具有多个参数：

| 参数名称 | 参数类型 | 注释 | 必须 |
|----------------|----------------|-------|----------|
| `name`         | String |规则名称最多只能包含 256 个字母数字字符。 规则名称区分大小写。 该名称必须在策略中唯一。 | True |
| `enabled`      | 布尔 | 一个允许暂时禁用规则的可选布尔值。 如果未设置，则默认值为 true。 | False | 
| `type`         | 枚举值 | 当前的有效类型为 `Lifecycle`。 | True |
| `definition`   | 定义生命周期规则的对象 | 每个定义均由筛选器集和操作集组成。 | True |

## <a name="rules"></a>规则

每个规则定义包括筛选器集和操作集。 [筛选器集](#rule-filters)将规则操作限制为容器或对象名称中的某组对象。 [操作集](#rule-actions)对筛选的对象集应用分层或删除操作。

### <a name="sample-rule"></a>示例规则

以下示例规则将筛选帐户，以针对 `container1` 中存在的、以 `foo` 开头的对象运行操作。

>[!NOTE]
>- 生命周期管理支持块 blob 和追加 blob 类型。<br>
>- 生命周期管理不会影响 $logs 和 $web 等系统容器。

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

| 筛选器名称 | 筛选器类型 | 注释 | 是否必需 |
|-------------|-------------|-------|-------------|
| blobTypes   | 预定义枚举值的数组。 | 当前版本支持 `blockBlob` 和 `appendBlob` 。 仅支持删除 `appendBlob` ，不支持集层。 | 是 |
| prefixMatch | 要匹配的前缀字符串数组。 每个规则最多可定义 10 个前缀。 前缀字符串必须以容器名称开头。 例如，如果要为某个规则匹配 `https://myaccount.blob.core.windows.net/container1/foo/...` 下的所有 Blob，则 prefixMatch 为 `container1/foo`。 | 如果未定义 prefixMatch，规则将应用到存储帐户中的所有 Blob。 | 否 |
| blobIndexMatch | 由要匹配的 Blob 索引标记键和值条件组成的字典值的数组。 每个规则最多可以定义10个 Blob 索引标记条件。 例如，如果想要将的所有 blob 与 `Project = Contoso` 下的 `https://myaccount.blob.core.windows.net/` 规则进行匹配，则 blobIndexMatch 为 `{"name": "Project","op": "==","value": "Contoso"}` 。 | 如果未定义 blobIndexMatch，则规则将应用于存储帐户中的所有 blob。 | 否 |

> [!NOTE]
> Blob 索引以公共预览版提供，在 **加拿大中部**、 **加拿大东部**、 **法国中部**和 **法国南部** 区域提供。 若要详细了解此功能以及已知问题和限制，请参阅[通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)。

### <a name="rule-actions"></a>规则操作

满足运行条件时，操作将应用到筛选的 Blob。

生命周期管理支持 Blob 的分层和删除，以及 Blob 快照的删除。 在 Blob 或 Blob 快照中为每个规则至少定义一个操作。

| 操作                      | 基本 Blob                                   | 快照      |
|-----------------------------|---------------------------------------------|---------------|
| tierToCool                  | 目前支持位于热层的 Blob         | 不支持 |
| enableAutoTierToHotFromCool | 支持当前位于冷层的 blob        | 不支持 |
| tierToArchive               | 目前支持位于热层或冷层的 Blob | 不支持 |
| 删除                      | 支持 `blockBlob` 和 `appendBlob`  | 支持     |

>[!NOTE]
>如果在同一 Blob 中定义了多个操作，生命周期管理将对该 Blob 应用开销最低的操作。 例如，操作 `delete` 的开销比 `tierToArchive` 更低。 操作 `tierToArchive` 的开销比 `tierToCool` 更低。

运行条件基于期限。 基本 Blob 使用上次修改时间来跟踪陈旧程度，Blob 快照使用快照创建时间来跟踪陈旧程度。

| 操作运行条件               | 条件值                          | 说明                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | 指示陈旧程度（天）的整数值 | 基本 Blob 操作的条件                                              |
| daysAfterCreationGreaterThan       | 指示陈旧程度（天）的整数值 | Blob 快照操作的条件                                          |
| daysAfterLastAccessTimeGreaterThan | 指示陈旧程度（天）的整数值 |  (预览) 启用 "上次访问时间" 时的基本 blob 操作的条件 |

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

### <a name="move-data-based-on-last-accessed-date-preview"></a>根据上次访问日期 (预览移动数据) 

您可以启用上次访问时间跟踪来记录您的 blob 的上次读取或写入时间。 你可以使用 "上次访问时间" 作为筛选器来管理 blob 数据的分层和保留。

" **上次访问** 时间" 选项在以下区域的预览中提供：

 - 法国中部
 - 加拿大东部
 - 加拿大中部

> [!IMPORTANT]
> 上次访问时间跟踪预览版仅适用于非生产。 生产服务级别协议 (SLA) 当前不可用。

若要使用 "**上次访问**时间" 选项，请在 "Azure 门户的"**生命周期管理**"页上选择"**访问跟踪已启用**"。

#### <a name="how-last-access-time-tracking-works"></a>上次访问时间跟踪的工作原理

启用上次访问时间跟踪后，在 `LastAccessTime` 读取或写入 blob 时会更新调用的 blob 属性。 [获取 Blob](/rest/api/storageservices/get-blob)操作被视为一种访问操作。 [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)、 [获取 blob 元数据](/rest/api/storageservices/get-blob-metadata)和 [获取 blob 标记](/rest/api/storageservices/get-blob-tags) 不是访问操作，因此不会更新上次访问时间。

若要最大程度地减少对读取访问延迟的影响，只需第一次读取最后24小时就会更新上次访问时间。 在24小时内的后续读取操作不会更新上次访问时间。 如果在读取之间修改了 blob，则最后一个访问时间是两个值中的最新值。

在以下示例中，如果 blob 在30天内未被访问，则会将其移动到冷存储。 此 `enableAutoTierToHotFromCool` 属性是一个布尔值，该值指示当某个 blob 在分层为冷后再次访问时，是否应自动将它从冷层分层为热。

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>存储帐户支持

上次访问时间跟踪可用于以下类型的存储帐户：

 - 常规用途 v2 存储帐户
 - 块 Blob 存储帐户
 - Blob 存储帐户

如果你的存储帐户是常规用途 v1 帐户，请使用 Azure 门户升级到常规用途 v2 帐户。

目前尚不支持已启用分层命名空间以与 Azure Data Lake Storage Gen2 一起使用的存储帐户。

#### <a name="pricing-and-billing"></a>定价和计费

每个上次访问时间更新被视为 [另一操作](https://azure.microsoft.com/pricing/details/storage/blobs/)。

### <a name="archive-data-after-ingest"></a>引入数据后对其进行存档

某些数据在云中保持空闲状态，并且在存储后很少（如果有）被访问。 以下生命周期策略已配置为在引入数据后立即对其进行存档。 此示例将容器 `archivecontainer` 中的存储帐户中的块 Blob 转移到存档层。 转移是通过在上次修改后的 0 天内处理 Blob 实现的：

> [!NOTE] 
> 建议将 blob 直接上传到存档层以提高效率。 可以将 [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) 或 [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) 的 x-ms-access-tier 标头用于 REST 版本 2018-11-09 和更新版本或我们的最新 Blob 存储客户端库。 

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

某些数据预期在创建后的数日或数月内过期。 可以将生命周期管理策略配置为：根据数据陈旧程度删除数据，以使数据过期。 以下示例中演示的策略删除超过 365 天的所有块 Blob。

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

### <a name="delete-data-with-blob-index-tags"></a>删除包含 Blob 索引标记的数据
某些数据应仅在明确标记为删除时过期。 你可以配置生命周期管理策略，使标记有 blob 索引键/值属性的数据过期。 下面的示例演示了一个策略，该策略删除标记有的所有块 blob `Project = Contoso` 。 若要详细了解 Blob 索引，请参阅[通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)。

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
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

## <a name="faq"></a>常见问题

**我创建了一个新策略，但操作为什么没有立即运行？**

平台每天运行一次生命周期策略。 配置策略后，某些操作可能需要在长达 24 小时之后才能首次运行。

**如果更新现有策略，运行操作需要多长时间？**

已更新的策略最多需要 24 小时才能生效。 策略生效后，最多可能需要 24 小时才能执行操作。 因此，策略操作最多可能需要 48 小时才能完成。

**我手动解冻了某个存档的 Blob，如何防止它暂时性地移回到存档层？**

将 Blob 从一个访问层移到另一个访问层后，其上次修改时间不会更改。 如果手动将存档的 Blob 解冻到热层，生命周期管理引擎会将它移回到存档层。 暂时禁用影响此 Blob 的规则可防止该 Blob 再次存档。 可以安全地将 Blob 移回到存档层时，重新启用该规则即可。 如果需要将 Blob 永久保留在热层或冷层，也可以将其复制到另一个位置。

## <a name="next-steps"></a>后续步骤

了解如何在意外删除数据后恢复数据：

- [Azure 存储 Blob 的软删除](../blobs/storage-blob-soft-delete.md)

了解如何通过 Blob 索引管理和查找数据：

- [在 Azure Blob 存储中管理和查找数据（包含 Blob 索引）](storage-manage-find-blobs.md)
