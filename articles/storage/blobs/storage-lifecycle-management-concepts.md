---
title: 管理 Azure 存储生命周期
description: 了解如何创建生命周期策略规则，以将陈旧数据从热存储转移到冷存储和存档层。
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 284a590a484052fdb7da2f03c6155078268b2aac
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211438"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>管理 Azure Blob 存储生命周期（预览版）

数据集具有独特的生命周期。 在生命周期的早期，人们经常访问某些数据。 但随着数据的老化，访问需求急剧下降。 有些数据在云中保持空闲状态，并且在存储后很少被访问。 有些数据在创建后的数日或者数月即会过期，还有一些数据集在其整个生存期会频繁受到读取和修改。 Azure Blob 存储生命周期管理（预览版）为 GPv2 和 Blob 存储帐户提供丰富的基于规则的策略。 可使用该策略将数据转移到适当的访问层，或在数据的生命周期结束时使数据过期。

生命周期管理策略允许：

- 将 Blob 转移到较冷的存储层（从热到冷、从热到存档，或者从冷到存档），以便针对性能和成本进行优化
- 删除生命周期已结束的 Blob
- 在存储帐户级别定义每天运行一次的规则
- 将规则应用到容器或 Blob 子集（使用前缀作为筛选器）

假设某个数据集在生命周期的早期阶段频繁被访问，两周后只是偶尔被访问。 一个月以后，该数据集很少被访问。 在这种场景下，早期阶段最适合使用热存储。 在偶尔访问阶段最适合使用冷存储，在一个月后数据陈旧时，存档存储便是最佳的层选项。 通过根据数据陈旧程度调整存储层，可根据需求设计出最具性价比的存储选项。 若要实现这种过渡，可以使用生命周期管理策略规则将陈旧数据转移到较冷的存储层。

## <a name="storage-account-support"></a>存储帐户支持

生命周期管理策略适用于常规用途 v2 (GPv2) 帐户和 Blob 存储帐户。 只需在 Azure 门户中执行简单的一键式过程，即可将现有的常规用途 (GPv1) 帐户升级为 GPv2 帐户。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。  

## <a name="pricing"></a>定价 

在预览版中，生命周期管理功能是免费的。 客户需要支付[列出 Blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 和[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 调用的常规操作费用。 有关定价的详细信息，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="register-for-preview"></a>注册预览版 
若要注册公共预览版，需要提交请求，将此功能注册到订阅中。 请求通常会在 72 小时内获得批准。 获得批准后，以下区域中的所有现有和新的 GPv2 或 Blob 存储帐户都将包含此功能：美国西部 2、美国中西部、美国东部 2 和西欧。 预览版仅支持块 Blob。 与大多数预览版一样，不应将此功能用于生产工作负荷，直到正式版发布。

若要提交请求，请运行以下 PowerShell 或 CLI 命令。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

提交请求：

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
可使用以下命令检查注册批准状态：
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
如果已批准并正确注册了此功能，则在提交先前的请求时会收到 *Registered* 状态。

### <a name="azure-cli"></a>Azure CLI

提交请求： 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
可使用以下命令检查注册批准状态：
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
如果已批准并正确注册了此功能，则在提交先前的请求时会收到 *Registered* 状态。


## <a name="add-or-remove-a-policy"></a>添加或删除策略 

可以通过 Azure 门户、[PowerShell](https://www.powershellgallery.com/packages/Az.Storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create)、[REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate) 或客户端工具使用以下语言来添加、编辑或删除策略：[.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview)、[Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/)、[Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0)、[Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2)。 

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“所有资源”，然后选择你的存储帐户。

3. 选择“Blob 服务”下面分组的“生命周期管理(预览版)”，以查看或更改策略。

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
如果为存储帐户启用了防火墙规则，生命周期管理请求可能会被阻止。 可以通过提供例外来取消阻止这些请求。 有关详细信息，请参阅[配置防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)中的“例外”部分。

## <a name="policy"></a>策略

生命周期管理策略是 JSON 文档中的规则集合：

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
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


一个策略需要两个参数：

| 参数名称 | 参数类型 | 说明 |
|----------------|----------------|-------|
| 版本        | 以 `x.x` 形式表示的字符串 | 预览版本号为 0.5。 |
| 规则          | 规则对象的数组 | 每个策略中至少需要一个规则。 在预览期，最多可为每个策略指定 4 个规则。 |

策略中的每个规则都需要三个参数：

| 参数名称 | 参数类型 | 说明 |
|----------------|----------------|-------|
| Name           | String | 规则名称只能包含字母数字字符的任意组合。 规则名称区分大小写。 该名称必须在策略中唯一。 |
| type           | 枚举值 | 预览版中的有效值为 `Lifecycle`。 |
| 定义     | 定义生命周期规则的对象 | 每个定义均由筛选器集和操作集组成。 |

## <a name="rules"></a>规则

每个规则定义包括筛选器集和操作集。 [筛选器集](#rule-filters)将规则操作限制为容器或对象名称中的某组对象。 [操作集](#rule-actions)对筛选的对象集应用分层或删除操作。

### <a name="sample-rule"></a>示例规则
以下示例规则将筛选帐户，以便仅对 `container1/foo` 运行操作。 对位于 `container1` 中**并且**以 `foo` 开头的所有对象运行以下操作： 

- 在上次修改后的 30 天后，将 Blob 分层到冷层
- 在上次修改后的 90 天后，将 Blob 分层到存档层
- 在上次修改后的 2,555 天（7 年）后，删除 Blob
- 在创建快照后的 90 天后，删除 Blob 快照

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
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

在预览期，有效的筛选器包括：

| 筛选器名称 | 筛选器类型 | 说明 | 是否必需 |
|-------------|-------------|-------|-------------|
| blobTypes   | 预定义枚举值的数组。 | 预览版仅支持 `blockBlob`。 | 是 |
| prefixMatch | 要匹配的前缀字符串数组。 前缀字符串必须以容器名称开头。 例如，如果要为某个规则匹配“https://myaccount.blob.core.windows.net/container1/foo/...”下的所有 Blob，则 prefixMatch 为 `container1/foo`。 | 如果未定义 prefixMatch，规则将应用到帐户中的所有 Blob。 | 否 |

### <a name="rule-actions"></a>规则操作

满足执行条件时，操作将应用到筛选的 Blob。

在预览版中，生命周期管理支持 Blob 的分层和删除，以及 Blob 快照的删除。 在 Blob 或 Blob 快照中为每个规则至少定义一个操作。

| 操作        | 基本 Blob                                   | 快照      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 目前支持位于热层的 Blob         | 不支持 |
| tierToArchive | 目前支持位于热层或冷层的 Blob | 不支持 |
| delete        | 支持                                   | 支持     |

>[!NOTE] 
如果在同一 Blob 中定义了多个操作，生命周期管理将对该 Blob 应用开销最低的操作。 例如，操作 `delete` 的开销比 `tierToArchive` 更低。 操作 `tierToArchive` 的开销比 `tierToCool` 更低。

在预览版中，操作执行条件基于陈旧程度。 基本 Blob 使用上次修改时间来跟踪陈旧程度，Blob 快照使用快照创建时间来跟踪陈旧程度。

| 操作执行条件 | 条件值 | 说明 |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | 指示陈旧程度（天）的整数值 | 基本 Blob 操作的有效条件 |
| daysAfterCreationGreaterThan     | 指示陈旧程度（天）的整数值 | Blob 快照操作的有效条件 | 

## <a name="examples"></a>示例
以下示例演示如何使用生命周期策略规则满足常见场景。

### <a name="move-aging-data-to-a-cooler-tier"></a>将陈旧的数据移到冷层

此示例演示如何转移前缀为 `container1/foo` 或 `container2/bar` 的块 Blob。 该策略将 30 天以上未修改的 Blob 转移到冷存储，并将 90 天未修改的 Blob 转移到存档层：

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

某些数据在云中保持空闲状态，并且在存储后很少（如果有）被访问。 此类数据应在引入后立即存档。 以下生命周期策略配置为在引入时存档数据。 此示例将容器 `archivecontainer` 中的存储帐户中的块 Blob 立即转移到存档层。 立即转移是通过在上次修改后的 0 天内处理 Blob 实现的：

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

某些数据应在创建后的数日或数月内过期，以降低成本或遵从政府的要求。 可以将生命周期管理策略配置为：根据数据陈旧程度删除数据，以使数据过期。 以下示例中演示的策略删除超过 365 天的所有块 Blob（未指定前缀）。

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>常见问题 - 我创建了一个新策略，为什么操作没有立即运行？ 

平台每天运行一次生命周期策略。 设置新策略后，最多可能需要 24 小时才能启动并运行某些操作（例如分层和删除）。  

## <a name="next-steps"></a>后续步骤

了解如何在意外删除数据后恢复数据：

- [Azure 存储 Blob 的软删除](../blobs/storage-blob-soft-delete.md)
