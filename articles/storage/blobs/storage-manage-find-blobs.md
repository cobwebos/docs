---
title: 在 Azure Blob 存储中管理和查找数据（预览版）
description: 了解如何使用 Blob 索引标记来分类、管理和查询以发现 Blob 对象。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f1a4d9af8a1b1095527078dd790e80ef45a5ee9a
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722890"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>在 Azure Blob 存储中管理和查找数据（预览版）

当数据集变得更大且更大时，在数据的汪洋大海中查找特定对象可能会很困难且令人沮丧。 Blob 索引使用键-值索引标记特性提供数据管理和发现功能，这允许您在单个容器中或存储帐户中的所有容器中对对象进行分类和查找。 以后在数据更改的要求中，可以通过在保持其当前容器组织的同时保留对象的索引标记的同时，动态重新分类对象。 利用 Blob 索引可以通过在同一服务中合并 Blob 数据和关联索引属性来简化开发工作;允许使用本机功能构建高效且可缩放的应用程序。 

Blob 索引允许：

- 使用键/值索引标记为数据管理动态分类 blob
- 跨单个容器或整个存储帐户快速查找特定标记 blob
- 基于索引标记的计算指定 blob Api 的条件行为
- 利用 blob 平台功能上高级控制的索引标记，如[生命周期管理](storage-lifecycle-management-concepts.md)

请考虑这样一种情形：你的存储帐户中有数百万个 blob，并且这些 blob 由许多不同的应用程序编写和访问。 要从单个项目中查找所有相关的数据，但不确定其作用域，因为数据可跨多个容器，具有不同的 blob 命名约定。 但知道，你的应用程序会基于其各自的项目和标识说明，上传包含标记的所有数据。 你只需使用`Project = Contoso`作为发现条件，而不必搜索数百万个 blob 和比较名称和属性。 Blob 索引将筛选整个存储帐户中的所有容器，以便快速查找并只返回来自`Project = Contoso`的 50 blob 集。 

若要开始使用 Blob 索引的示例，请参阅[利用 Blob 索引管理和查找数据](storage-blob-index-how-to.md)。

## <a name="blob-index-tags-and-data-management"></a>Blob 索引标记和数据管理

容器和 blob 名称前缀是存储的数据的一维分类。 Blob 索引现在允许包含应用的属性标记的所有[blob 数据类型（块、追加或页）](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)具有多维度分类。 此多维分类是本机索引的，可供您快速查询和查找数据。

请考虑存储帐户中的以下五个 blob：
>
> container1/事务 .csv  
> container2/  
> 照片/bannerphoto  
> 存档/已完成/2019review  
> 日志/2020/01/01/logfile .txt  
>

这些 blob 目前使用容器/虚拟文件夹/blob 名称的前缀进行分隔。 使用 Blob 索引，可以将这五个 blob 上的`Project = Contoso`索引标记属性设置为在一起，同时保留其当前的前缀组织。 这样就无需通过使用存储平台的多维索引来筛选和查找数据，从而无需移动数据。

## <a name="setting-blob-index-tags"></a>设置 Blob 索引标记

Blob 索引标记是可应用于存储帐户中的新对象或现有对象的键-值属性。 使用 PutBlob、PutBlockList 或 CopyBlob 操作和可选的 x 毫秒标记标头，可以在上载过程中指定索引标记。 如果你的存储帐户中已有 blob，则可使用格式化的 XML 文档（在请求正文中指定 blob 索引标记属性）调用 SetBlobTags。 

请考虑以下可以设置的示例标记

你可以在 blob 上应用单个标记来描述你的数据处理完成的时间。
>
> "processedDate" = "2020-01-01"
>
可以在 blob 上应用多个标记，以便更好地说明数据。
>
> "项目" = "Contoso"  
> "已分类" = "True"  
> "状态" = "未处理"  
> "Priority" = "01" 
>

若要修改现有的索引标记特性，必须首先检索现有标记特性，修改标记特性，并将替换为 SetBlobTags 操作。 若要从 blob 中删除所有索引标记，请调用 SetBlobTags 操作，不指定标记属性。 Blob 索引标记是 blob 数据内容的子资源，SetBlobTags 不会修改任何基础内容，也不会更改 blob 的上次修改时间。

以下限制适用于 Blob 索引标记：
- 每个 blob 最多可以有10个 blob 索引标记
- 标记键的长度必须介于1到128个字符之间
- 标记值必须介于0到256个字符之间
- 标记键和值区分大小写
- 标记键和值仅支持字符串数据类型;所有数字或特殊字符都将保存为字符串
- 标记键和值必须符合以下命名规则：
  - 字母数字字符： a-z、a-z、0-9
  - 特殊字符：空格、加、减、句点、冒号、等于、下划线、正斜杠

## <a name="getting-and-listing-blob-index-tags"></a>获取和列出 Blob 索引标记

Blob 索引标记作为子资源与 blob 数据一起存储，可独立于基础 blob 数据内容进行检索。 设置后，可以立即使用 GetBlobTags 操作检索和查看单个 blob 的 blob 索引标记。 带`include:tags`参数的 ListBlobs 操作还会返回容器中的所有 blob 及其应用的 blob 索引标记。 

对于至少具有1个 blob 索引标记的任何 blob，将在 ListBlobs、GetBlob 和 GetBlobProperties 操作中返回 x ms 标记计数，指示 blob 上存在的 blob 索引标记的计数。

## <a name="finding-data-using-blob-index-tags"></a>使用 Blob 索引标记查找数据

在 blob 上设置 blob 索引标记后，索引引擎会将这些键/值属性公开为多维索引。 虽然索引标记存在于 blob 上并且可以立即检索，但在使用刷新的索引标记特性更新 blob 索引之前，可能需要一段时间。 更新 blob 索引后，现在可以利用 blob 存储提供的本机查询和发现功能。

使用 FindBlobsByTags 操作可以获取筛选后的一组 blob，其索引标记与给定的 blob 索引查询表达式匹配。 Blob 索引支持在存储帐户中的所有容器之间进行筛选，也可将筛选限定为单个容器。 由于所有 blob 索引标记键和值都是字符串，因此支持的关系运算符对索引标记值使用字典排序。

以下条件适用于 blob 索引筛选：
-   标记键应括在双引号（"）中
-   标记值和容器名称应括在单引号（'）中
-   仅允许在特定容器名称（即@container = "容器名称"）上进行筛选的 @ 字符
- 筛选器应用于字符串的字典排序
-   同一键上的相同面范围操作无效（即 "Rank" > "10" 和 "Rank" >= "15"）
- 使用 REST 创建筛选器表达式时，应对字符进行 URI 编码

下表显示了 FindBlobsByTags 的所有有效运算符：

|  运算符  |  说明  | 示例 |
|------------|---------------|---------|
|     =      |     等于     | "状态" = "正在进行" | 
|     >      |  大于 |  "日期" > "2018-06-18" |
|     >=     |  大于或等于 | "Priority" >= "5" | 
|     <      |  小于    | "Age" < "32" |
|     <=     |  小于或等于  | "公司" <= "Contoso" |
|    AND     |  逻辑与  | "Rank" >= "010" 和 "Rank" < "100" |
| @container |  作用域到特定容器   | @container= ' videofiles ' 和 "status" = ' done ' |

## <a name="conditional-blob-operations-with-blob-index-tags"></a>带有 Blob 索引标记的条件 Blob 操作
在 REST 版本2019-10-10 及更高版本中，大多数[blob 服务 api](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)现在支持条件标头（x-毫秒-标记），以便仅当满足指定的 blob 索引条件时，操作才会成功。 如果不满足条件，则将获得`error 412: The condition specified using HTTP conditional header(s) is not met`。

"X-毫秒-标记" 标头可以与其他现有 HTTP 条件标头（If-Match、If-match 等）组合。  如果请求中提供了多个条件标头，则它们都必须评估为 true，操作才能成功。  所有条件标头都与逻辑 AND 组合在一起。 

下表显示了条件运算的所有有效运算符：

|  运算符  |  说明  | 示例 |
|------------|---------------|---------|
|     =      |     等于     | "状态" = "正在进行" |
|     <>     |   不等于   | "状态"  <>  "完成"  | 
|     >      |  大于 |  "日期" > "2018-06-18" |
|     >=     |  大于或等于 | "Priority" >= "5" | 
|     <      |  小于    | "Age" < "32" |
|     <=     |  小于或等于  | "公司" <= "Contoso" |
|    AND     |  逻辑与  | "Rank" >= "010" 和 "Rank" < "100" |
|     或     |  逻辑或   | "状态" = "完成" 或 "优先级" >= "05" |

> [!NOTE]
> 有两个额外的运算符（不等于和逻辑或）可用于 blob 操作的条件 x ms-chap 标头中，但在 FindBlobsByTags 操作中不存在。

## <a name="platform-integrations-with-blob-index-tags"></a>具有 Blob 索引标记的平台集成

Blob 索引的标记不仅有助于对 blob 数据进行分类、管理和搜索，还能提供与其他 Blob 服务功能（如[生命周期管理](storage-lifecycle-management-concepts.md)）的集成。 

### <a name="lifecycle-management"></a>生命周期管理

使用新的 blobIndexMatch 作为生命周期管理中的规则筛选器，可将数据移到冷层，或基于应用于 blob 的索引标记删除数据。 这样一来，你可以在规则中更详细地了解规则，并且仅当数据与指定的标记条件匹配时才移动或删除数据。

您可以将 blob 索引匹配设置为生命周期规则中的独立筛选器集，以便对标记的数据应用操作。 也可以组合使用前缀匹配和 blob 索引匹配来匹配更具体的数据集。 将多个筛选器应用于生命周期规则的操作是一种逻辑 AND 操作，以便仅当所有筛选条件匹配时才应用该操作。 

以下示例生命周期管理规则适用于容器 "videofiles" 中的块 blob，以及用于仅在数据与的 blob 索引标记条件匹配时用于存档存储```"Status" = 'Processed' AND "Source" == 'RAW'```的层 blob。

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Azure 门户中的生命周期管理的 Blob 索引匹配规则示例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>权限和授权

您可以使用以下方法之一来授权对 Blob 索引的访问：

- 通过使用基于角色的访问控制（RBAC）向 Azure Active Directory （Azure AD）安全主体授予权限。 Microsoft 建议使用 Azure AD，以实现更高的安全性和易用性。 有关将 Azure AD 与 blob 操作一起使用的详细信息，请参阅[使用 Azure Active Directory 授予对 blob 和队列的访问权限](../common/storage-auth-aad.md)。
- 使用共享访问签名（SAS）委派对 blob 索引的访问权限。 有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。
- 通过使用帐户访问密钥向使用共享密钥的操作授权。 有关详细信息，请参阅[通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)。

Blob 索引标记是 blob 数据的子资源。 具有权限的用户或用于读取或写入 blob 的 SAS 令牌可能无权访问 blob 索引标记。 

### <a name="role-based-access-control"></a>基于角色的访问控制 
可以向使用[AAD 标识](../common/storage-auth-aad.md)的调用方授予对 blob 索引标记进行操作的以下权限。 

|   Blob 操作   |  RBAC 操作   |
|---------------------|----------------|
| 按标记查找 Blob  | StorageAccounts/blobServices/容器/blob/筛选器 |
| 设置 Blob 标记         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | 
| 获取 Blob 标记         | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read |

对标记进行操作时，需要与基础 blob 数据分离的其他权限。 将为存储 Blob 数据参与者角色授予这三个权限。 将向存储 Blob 数据读取器授予仅按标记查找 Blob 和获取 Blob 标记权限的权限。

### <a name="sas-permissions"></a>SAS 权限 
可以向使用[共享访问签名（SAS）](../common/storage-sas-overview.md)的调用方授予对 blob 标记进行操作的作用域权限。
#### <a name="blob-sas"></a>Blob SAS
可以在 Blob 服务 SAS 中授予下列权限，以允许访问 blob 索引标记。 仅 Blob 读取和写入权限不足以允许读取或写入其索引标记。

|  权限  |  URI 符号  | 允许的操作 |
|--------------|--------------|--------------------|
|  索引标记  |      t         | 获取和设置 blob 的 blob 索引标记 |

#### <a name="container-sas"></a>容器 SAS
可以在容器服务 SAS 中授予下列权限，以允许对 blob 标记进行筛选。  Blob 列表权限不足，无法按索引标记允许筛选 blob。

|  权限  |  URI 符号  | 允许的操作 |
|--------------|--------------|--------------------|
| 索引标记     |      f       | 查找包含 blob 索引标记的 Blob | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>在元数据和 Blob 索引标记之间选择 
Blob 索引标记和元数据都可以将任意用户定义的键/值属性与 Blob 资源一起存储。 两者都可以直接检索和设置，而不会返回或更改 blob 的内容。 可以使用元数据和索引标记。

但是，仅 Blob 索引标记会自动编制索引，并可由本机 Blob 服务进行查询。 除非使用了单独的服务（如[Azure 搜索](../../search/search-blob-ai-integration.md)），否则无法对元数据进行本机索引和查询。 Blob 索引标记还有其他用于读取/筛选和编写的权限，它们与基础 blob 数据分离。 元数据利用与 blob 相同的权限，并在 GetBlob 或 GetBlobProperties 操作中以 HTTP 标头的形式返回。 Blob 索引标记使用[Microsoft 托管的密钥](../common/storage-service-encryption.md)进行静态加密，而使用为 blob 数据指定的同一加密密钥对元数据进行静态加密。 

下表总结了 Metadata 和 Blob 索引标记之间的差异：

|              |   元数据   |   Blob 索引标记  |
|--------------|--------------|--------------------|
| **限制**         | 无数值限制;总共 8 KB;不区分大小写 | 每个 blob 最大10个标记;每个标记768字节;区分大小写 |
| **更新**      | 在存档层上不允许;SetBlobMetadata 替换所有现有元数据;SetBlobMetadata 更改了 blob 的上次修改时间 | 允许用于所有访问层;SetBlobTags 替换所有现有标记;SetBlobTags 不会更改 blob 的上次修改时间 |
| **存储**        | 与 blob 数据存储在一起 |  Blob 数据的子资源 | 
| **索引 & 查询** | 不适用;必须使用单独的服务，如 Azure 搜索 | 是的，本机索引和查询内置于 blob 存储中的功能 |
| **加密** | 使用与 blob 数据相同的加密密钥加密的静态 |  使用 Microsoft 托管的加密密钥进行静态加密 |
| **定价**   | 元数据的大小包含在 blob 的存储成本中 |    固定成本/索引标记 | 
| **标头响应** | 作为 GetBlob 和 GetBlobProperties 中的标头返回的元数据 | TagCount 返回 GetBlob 或 GetBlobProperties;仅 GetBlobTags 和 ListBlobs 中返回的标记 |
| **权限**  |    对 blob 数据的读取或写入权限扩展到元数据 |    读取/筛选或写入标记需要其他权限 |

## <a name="pricing"></a>定价
Blob 索引定价当前提供公共预览版，并可能会因公开上市而发生更改。 客户需要为存储帐户中的 blob 索引标记总数收费，每月求平均值。 索引引擎不会产生任何费用。 对 SetBlobTags、GetBlobTags 和 FindBlobsByTags 的请求按其各自的操作类型收费。 有关[详细信息，请参阅阻止 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="regional-availability-and-storage-account-support"></a>区域可用性和存储帐户支持

Blob 索引当前仅可用于常规用途 v2 （GPv2）帐户。 在 Azure 门户中，可将现有的常规用途 (GPv1) 帐户升级为 GPv2 帐户。 有关存储帐户的详细信息，请参阅[Azure 存储帐户概述](../common/storage-account-overview.md)。

在公共预览版中，Blob 索引当前仅在以下选择区域提供：
- 法国中部
- 法国南部

若要开始，请参阅[利用 Blob 索引来管理和查找数据](storage-blob-index-how-to.md)。

> [!IMPORTANT]
> 请参阅本文的 "条件" 一节。 若要注册预览版，请参阅本文的注册订阅部分。 在存储帐户上使用 Blob 索引之前，必须注册订阅。

### <a name="register-your-subscription-preview"></a>注册订阅（预览版）
由于 Blob 索引仅用于公共预览，因此你需要先注册你的订阅，然后才能使用该功能。 若要提交请求，请运行以下 PowerShell 或 CLI 命令。

#### <a name="register-by-using-powershell"></a>使用 PowerShell 注册
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>使用 Azure CLI 注册
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>条件和已知问题（预览版）
本部分介绍 Blob 索引当前公共预览版中的已知问题和条件。 与大多数预览一样，此功能不应用于生产工作负荷，因为行为可能会发生变化。

-   对于预览版，你必须先注册你的订阅，然后才能将 Blob 索引用于预览区域中的存储帐户。
-   预览版目前仅支持 GPv2 帐户。 Blob 索引当前不支持 blob、BlockBlobStorage 和 HNS enabled DataLake Gen2 帐户。
-   上载包含索引标记的页 blob 当前不会保留标记。 必须在上载页 blob 后设置标记。
-   当筛选的作用域限定为单个容器时@container ，只有在筛选器表达式中的所有索引标记均为相等检查（key = value）时，才能传递。 
-   将 range 运算符与 AND 条件一起使用时，只能指定相同的索引标记键名称（Age > "013" 和 Age < "100"）。
-   当前不支持版本控制和 Blob 索引。 Blob 索引标记保留用于版本，但当前未传递到 blob 索引引擎。
-   当前不支持帐户故障转移。 Blob 索引在故障转移后可能不会正确更新。
-   生命周期管理当前仅支持 Blob 索引匹配的相等性检查。
-   CopyBlob 不会将 blob 索引标记从源 blob 复制到新的目标 blob。 你可以指定要在复制操作过程中应用于目标 blob 的标记。 
-   在创建快照时保留标记;但当前不支持升级快照，并且可能会导致空标记集。

## <a name="faq"></a>常见问题解答

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Blob 索引可以帮助我筛选和查询 blob 中的内容吗？ 
不可以，Blob 索引标记可帮助你查找要查找的 blob。 如果需要在 blob 内搜索，请使用查询加速或 Azure 搜索。

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Blob 索引标记和 Azure 资源管理器标记是相关的吗？
不能，Azure 资源管理器标记有助于组织控制平面资源，如订阅、资源组和存储帐户。 Blob 索引标记提供对数据平面资源（如存储帐户中的 blob）的对象管理和发现。

## <a name="next-steps"></a>后续步骤

请参阅如何使用 Blob 索引的示例。 请参阅[利用 Blob 索引管理和查找数据](storage-blob-index-how-to.md)

