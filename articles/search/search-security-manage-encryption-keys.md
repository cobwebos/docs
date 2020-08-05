---
title: 使用客户管理的密钥进行静态加密
titleSuffix: Azure Cognitive Search
description: 使用在 Azure Key Vault 中创建和管理的密钥，来补充 Azure 认知搜索中基于索引和同义词映射的服务器端加密。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: ed5d1f5b35bc9b6dee234678fa82af95e1d53bc7
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553984"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>在 Azure 中配置客户管理的密钥以进行数据加密认知搜索

Azure 认知搜索会自动用[服务托管的密钥](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)加密静态的索引内容。 如果需要更多的保护，可以使用在 Azure Key Vault 中创建和管理的密钥来补充使用额外加密层的默认加密。 本文将指导你完成设置 CMK 加密的步骤。

CMK 加密依赖于[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 在 Azure Key Vault 中，如果[启用日志记录](../key-vault/general/logging.md)，还可以审核密钥用法。  

当创建这些对象时，将对客户托管的密钥进行加密，将其应用于各个索引或同义词映射，并且不会在搜索服务级别上指定。 只能加密新的对象。 无法加密已存在的内容。

密钥不需要位于同一密钥保管库中。 单个搜索服务可以托管多个加密索引或同义词映射，每个索引使用自己的客户托管的加密密钥进行加密，并存储在不同的密钥保管库中。 还可以在同一服务中托管未使用客户管理的密钥加密的索引和同义词映射。 

## <a name="double-encryption"></a>双加密

对于在2020年8月1日之后创建的服务，以及在特定区域中创建的服务，CMK 加密的作用域包括临时磁盘，从而实现[完全双重加密](search-security-overview.md#double-encryption)（当前在这些区域中提供）： 

+ 美国西部 2
+ 美国东部
+ 美国中南部
+ US Gov 弗吉尼亚州
+ US Gov 亚利桑那州

如果你使用的是其他区域，或在8月1日之前创建的服务，则 CMK 加密仅限于数据磁盘，不包括服务使用的临时磁盘。

## <a name="prerequisites"></a>先决条件

本示例中使用了以下服务和服务。 

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 

+ [创建 Azure Key Vault 资源](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)，或在 Azure 认知搜索所在的同一订阅中查找现有保管库。 此功能具有相同的订阅要求。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 用于配置任务。

+ [Postman](search-get-started-postman.md)、 [AZURE POWERSHELL](search-create-index-rest-api.md)和[.net SDK preview](https://aka.ms/search-sdk-preview)可用于调用创建包含加密密钥参数的索引和同义词映射的 REST API。 目前尚不支持将键添加到索引或同义词映射。

>[!Note]
> 由于与客户管理的密钥加密的性质，如果 Azure 密钥保管库密钥被删除，Azure 认知搜索将无法检索数据。 若要防止意外 Key Vault 删除密钥导致的数据丢失，必须在密钥保管库上启用软删除和清除保护。 默认情况下会启用软删除，因此，只有在你特意禁用了此功能时才会遇到问题。 清除保护默认情况下未启用，但 Azure 认知搜索 CMK 加密是必需的。 有关详细信息，请参阅[软删除](../key-vault/key-vault-ovw-soft-delete.md)和[清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)概述。

## <a name="1---enable-key-recovery"></a>1 - 启用密钥恢复

Key vault 必须启用**软删除**和**清除保护**。 可以使用门户或以下 PowerShell 或 Azure CLI 命令设置这些功能。

### <a name="using-powershell"></a>使用 PowerShell

1. 运行 `Connect-AzAccount` 设置 Azure 凭据。

1. 运行以下命令以连接到密钥保管库， `<vault_name>` 并使用有效名称替换：

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault 是在启用软删除的情况创建的。 如果在保管库中禁用了此功能，请运行以下命令：

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 启用清除保护：

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 保存更新：

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>使用 Azure CLI

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - 创建新密钥

如果使用现有密钥来加密 Azure 认知搜索内容，请跳过此步骤。

1. [登录到 Azure 门户](https://portal.azure.com)并打开 Key Vault 概述页。

1. 在左侧导航窗格中选择“密钥”设置，然后单击“+ 生成/导入”。 

1. 在“创建密钥”窗格中，从“选项”列表中，选择要用于创建密钥的方法。 可以**生成**新密钥、**上传**现有密钥，或使用“备份还原”选择密钥的备份。

1. 输入密钥的**名称**，并根据需要选择其他密钥属性。

1. 单击“创建”按钮开始部署。

请记下密钥标识符 – 它由**密钥值 URI**、**密钥名称**和**密钥版本**构成。 稍后需要使用这些信息来定义 Azure 认知搜索中已加密的索引。
 
![创建新的 Key Vault 密钥](./media/search-manage-encryption-keys/create-new-key-vault-key.png "创建新的 Key Vault 密钥")

## <a name="3---create-a-service-identity"></a>3 - 创建服务标识

将标识分配给搜索服务可以授予 Key Vault 对搜索服务的访问权限。 搜索服务将使用其标识对 Azure Key Vault 进行身份验证。

Azure 认知搜索支持通过两种方式来分配标识：托管标识，或外部托管的 Azure Active Directory 应用程序。 

如果可能，请使用托管标识。 它是将标识分配给搜索服务的最简单方式，应该可在大多数方案中使用。 如果你对索引和同义词映射使用多个密钥，或者解决方案位于不符合基于标识的身份验证条件的分布式体系结构中，请使用本文末尾所述的高级[外部托管 Azure Active Directory 方法](#aad-app)。

 一般情况下，搜索服务可以使用托管标识对 Azure Key Vault 进行身份验证，而无需在代码中存储凭据。 此类托管标识的生命周期与只包含一个托管标识的搜索服务的生命周期密切相关。 [详细了解托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

1. [登录到 Azure 门户](https://portal.azure.com)并打开搜索服务概述页。 

1. 在左侧导航窗格中单击“标识”，将其状态更改为“开”，然后单击“保存”。  

![启用托管标识](./media/search-enable-msi/enable-identity-portal.png "启用托管标识")

## <a name="4---grant-key-access-permissions"></a>4 - 授予密钥访问权限

若要使搜索服务能够使用 Key Vault 密钥，需要向搜索服务授予特定的访问权限。

可在任意给定时间撤销访问权限。 撤销后，使用该 Key Vault 的任何搜索服务索引或同义词映射都将不可用。 以后还原 Key Vault 访问权限会还原索引/同义词映射访问权限。 有关详细信息，请参阅[保护对 Key Vault 的访问](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

1. [登录到 Azure 门户](https://portal.azure.com)并打开 Key Vault 概述页。 

1. 从左侧导航窗格中选择 "**访问策略**" 设置，然后单击 " **+ 添加新**"。

   ![添加新的 Key Vault 访问策略](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "添加新的 Key Vault 访问策略")

1. 单击“选择主体”并选择你的 Azure 认知搜索服务。 可以根据启用托管标识后显示的名称或对象 ID 来搜索该搜索服务。

   ![选择 Key Vault 访问策略主体](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "选择 Key Vault 访问策略主体")

1. 单击“密钥权限”，然后选择“获取”、“解包密钥”和“包装密钥”。   可以使用 Azure Data Lake Storage 或 Azure 存储模板快速选择所需的权限。

   必须向 Azure 认知搜索授予以下[访问权限](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)：

   * 获取 - 可让搜索服务检索 Key Vault 中密钥的公共部分
   * 包装密钥 - 可让搜索服务使用密钥来保护内部加密密钥
   * 解包密钥 - 可让搜索服务使用密钥来解包内部加密密钥

   ![选择 Key Vault 访问策略密钥权限](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "选择 Key Vault 访问策略密钥权限")

1. 对于**密钥权限**，请选择 "*获取*"。

1. 对于**证书权限**，请选择 "*获取*"。

1. 单击“确定”，然后**保存**访问策略更改。

> [!Important]
> Azure 认知搜索中已加密的内容配置为使用特定**版本**的特定 Azure Key Vault 密钥。 如果你更改密钥或版本，必须先将索引或同义词映射更新为使用新的密钥/版本，**然后**删除以前的密钥/版本。 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。   

## <a name="5---encrypt-content"></a>5 - 加密内容

若要在索引或同义词映射上添加客户托管的密钥，必须使用[搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)或 SDK。 门户不会公开同义词映射或加密属性。 使用有效的 API 时，索引和同义词映射都支持顶级**encryptionKey**属性。 

使用密钥保管**库 Uri**、**密钥名称**和密钥保管库密钥的**密钥版本**，创建**encryptionKey**定义，如下所示：

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> 这些 Key Vault 详细信息都不被视为机密，在 Azure 门户中浏览到相关的 Azure Key Vault 密钥页即可轻松检索这些信息。

如果使用 AAD 应用程序而不是托管标识进行 Key Vault 身份验证，请将 AAD 应用程序**访问凭据**添加到加密密钥： 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>示例：索引加密
[创建索引（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)中提供了有关通过 REST API 创建新索引的详细信息。本文的唯一差别在于，需要将加密密钥详细信息指定为索引定义的一部分： 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
现在，可以发送索引创建请求，然后开始正常使用索引。

## <a name="example-synonym-map-encryption"></a>示例：同义词映射加密

[创建同义词映射（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)中提供了有关通过 REST API 创建新同义词映射的详细信息。本文的唯一差别在于，需要将加密密钥详细信息指定为同义词映射定义的一部分： 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
现在，可以发送同义词映射创建请求，然后开始正常使用同义词映射。

>[!Important] 
> 尽管无法将 **encryptionKey** 添加到现有的 Azure 认知搜索索引或同义词映射，但可以通过为三项 Key Vault 详细信息的任何一项提供不同的值，来更新该属性（例如，更新密钥版本）。 更改为新的 Key Vault 密钥或新的密钥版本时，必须先将使用该密钥的任何 Azure 认知搜索索引或同义词映射更新为使用新的密钥/版本，**然后**删除以前的密钥/版本。 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。   
> 以后还原 Key Vault 访问权限会还原内容访问权限。

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>高级：使用外部托管的 Azure Active Directory 应用程序

如果无法使用托管标识，可以使用 Azure 认知搜索服务的安全主体创建一个 Azure Active Directory 应用程序。 具体而言，在以下情况下无法使用托管标识：

* 无法直接授予搜索服务对 Key Vault 的访问权限（例如，如果搜索服务与 Azure Key Vault 位于不同的 Active Directory 租户中）。

* 必须使用单个搜索服务来托管多个已加密的索引/同义词映射，其中每个索引/同义词映射使用不同 Key Vault 中的不同密钥，而每个 Key Vault 必须使用**不同的标识**进行身份验证。 如果不要求使用不同的标识来管理不同的 Key Vault，请考虑使用上述托管标识选项。  

为了适应这种拓扑，Azure 认知搜索支持使用 Azure Active Directory (AAD) 应用程序在搜索服务与 Key Vault 之间进行身份验证。    
若要在门户中创建 AAD 应用程序：

1. [创建 Azure Active Directory 应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [获取应用程序 ID 和身份验证密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，因为创建加密的索引时需要用到这些信息。 需要提供的值包括**应用程序 ID** 和**身份验证密钥**。

>[!Important]
> 决定使用 AAD 应用程序而不是托管标识进行身份验证时，请考虑到以下事实：Azure 认知搜索无权代表你管理 AAD 应用程序，需要由你自己管理 AAD 应用程序，例如，定期轮换应用程序身份验证密钥。
> 更改 AAD 应用程序或其身份验证密钥时，必须先将使用该应用程序的任何 Azure 认知搜索索引或同义词映射更新为使用新的应用程序 ID/密钥，**然后**删除以前的应用程序或其授权密钥，然后再撤消 Key Vault 对它的访问权限。
> 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。

## <a name="work-with-encrypted-content"></a>使用加密内容

使用 CMK 加密，你会注意到由于额外的加密/解密工作而导致索引和查询的延迟。 Azure 认知搜索不记录加密活动，但你可以通过 key vault 日志记录监视密钥访问。 建议在设置密钥保管库的过程中[启用日志记录](../key-vault/general/logging.md)。

应在一段时间内发生密钥轮换。 旋转键时，请务必遵循此顺序：

1. [确定索引或同义词映射使用的键](search-security-get-encryption-keys.md)。
1. [在 key vault 中创建新密钥](../key-vault/keys/quick-create-portal.md)，但保留原始密钥可用。
1. 更新索引或同义词映射上[的 encryptionKey 属性](https://docs.microsoft.com/rest/api/searchservice/update-index)，以使用新值。 只能更新最初使用此属性创建的对象以使用其他值。
1. 禁用或删除密钥保管库中的上一个密钥。 监视密钥访问以验证是否正在使用新密钥。

出于性能方面的考虑，搜索服务会缓存此密钥长达几个小时。 如果在不提供新密钥的情况下禁用或删除该密钥，则查询将继续暂时工作，直到缓存过期。 但是，一旦搜索服务无法解密内容，你就会收到以下消息： "访问被禁止。 使用的查询密钥可能已被吊销-请重试。 " 

## <a name="next-steps"></a>后续步骤

如果你不熟悉 Azure 安全体系结构，请查看 [Azure 安全文档](https://docs.microsoft.com/azure/security/)，具体而言，是以下文章：

> [!div class="nextstepaction"]
> [静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
