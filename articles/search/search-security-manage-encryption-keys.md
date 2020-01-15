---
title: 使用客户托管密钥进行静态加密
titleSuffix: Azure Cognitive Search
description: 使用在 Azure Key Vault 中创建和管理的密钥，对 Azure 中的索引和同义词映射补充服务器端加密认知搜索。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 6c7be7d92cae992e54ca6e9f50dda6342c57856b
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945713"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Azure 中的内容的静态加密认知搜索在 Azure Key Vault 中使用客户托管的密钥

默认情况下，Azure 认知搜索使用[服务托管密钥](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)来加密静态的索引内容。 你可以使用在 Azure Key Vault 中创建和管理的密钥来补充使用额外加密层的默认加密。 本文将指导你完成这些步骤。

通过与[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)的集成支持服务器端加密。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 使用 Azure Key Vault，还可以审核密钥用法。 

当创建这些对象时，将在索引或同义词映射级别配置对客户托管密钥的加密，而不是在搜索服务级别配置。 不能加密已存在的内容。 

密钥并不需要在同一 Key Vault 中。 单个搜索服务可以托管多个已加密的索引或同义词映射，每个加密的加密密钥存储在不同的密钥保管库中。  你还可以在未使用客户管理的密钥加密的同一服务中包含索引和同义词映射。 

> [!IMPORTANT] 
> 此功能在[REST API 版本 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/)和[.net SDK 8.0 版-预览版](search-dotnet-sdk-migration-version-9.md)中可用。 当前不支持在 Azure 门户中配置客户管理的加密密钥。

## <a name="prerequisites"></a>必备组件

本示例中使用了以下服务。 

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 搜索服务必须在2019年1月之后创建，且不能是免费（共享）服务。

+ [创建 Azure Key Vault 资源](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)，或查找订阅下的现有保管库。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)或[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)用于配置任务。

+ 可以使用[Postman](search-get-started-postman.md)、 [Azure PowerShell](search-create-index-rest-api.md)和[Azure 认知搜索 SDK](https://aka.ms/search-sdk-preview)来调用 REST API。 目前没有对客户管理的加密的门户支持。

>[!Note]
> 由于通过客户托管密钥功能进行加密的性质，Azure 认知搜索将无法检索你的数据（如果你的 Azure 密钥保管库密钥已删除）。 若要防止意外 Key Vault 删除密钥导致的数据丢失，**必须**先在 Key Vault 中启用软删除和清除保护，然后才能使用。 有关详细信息，请参阅[Azure Key Vault 软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。   

## <a name="1---enable-key-recovery"></a>1-启用密钥恢复

创建 Azure Key Vault 资源后，通过执行以下 PowerShell 或 Azure CLI 命令在所选密钥保管库中启用**软删除**和**清除保护**：   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-创建新密钥

如果使用现有密钥加密 Azure 认知搜索内容，请跳过此步骤。

1. [登录到 Azure 门户](https://portal.azure.com)并导航到密钥保管库仪表板。

1. 从左侧导航窗格中选择 "**密钥**" 设置，然后单击 " **+ 生成/导入**"。

1. 在 "**创建密钥**" 窗格中，从**选项**列表中选择要用于创建密钥的方法。 你可以**生成**新的密钥、**上传**现有密钥，或使用**还原备份**来选择密钥的备份。

1. 输入密钥的**名称**，并选择 "其他密钥属性"。

1. 单击 "**创建**" 按钮以开始部署。

记下密钥标识符–这是由**键值 Uri**、**密钥名称**和**密钥版本**组成的。 在 Azure 认知搜索中定义加密索引时需要使用这些项。
 
![创建新的 key vault 密钥](./media/search-manage-encryption-keys/create-new-key-vault-key.png "创建新的 key vault 密钥")

## <a name="3---create-a-service-identity"></a>3-创建服务标识

向搜索服务分配标识使你可以授予对搜索服务的 Key Vault 访问权限。 搜索服务将使用其标识对 Azure 密钥保管库进行身份验证。

Azure 认知搜索支持两种分配标识的方法：托管标识或外部托管 Azure Active Directory 应用程序。 

如果可能，请使用托管标识。 最简单的方法是将标识分配给搜索服务，并在大多数情况下使用。 如果将多个键用于索引和同义词映射，或者解决方案位于将使基于标识的身份验证的分布式体系结构中，请使用本文末尾介绍的 "高级[外部托管的 Azure Active Directory 方法](#aad-app)。

 通常，托管标识使搜索服务能够在不在代码中存储凭据的情况下进行身份验证 Azure Key Vault。 此类托管标识的生命周期绑定到搜索服务的生命周期，该生命周期只能有一个托管标识。 [了解有关托管标识的详细信息](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

1. 若要创建托管标识，请[登录权限门户](https://portal.azure.com)并打开搜索服务仪表板。 

1. 单击左侧导航窗格中的 "**标识**"，将其状态更改为 **"打开**"，然后单击 "**保存**"。

![启用托管标识](./media/search-enable-msi/enable-identity-portal.png "启用托管标识")

## <a name="4---grant-key-access-permissions"></a>4-授予密钥访问权限

若要使搜索服务能够使用您的 Key Vault 密钥，则需要向搜索服务授予某些访问权限。

在任何给定时间，都可以撤销访问权限。 一旦撤消，任何使用该密钥保管库的搜索服务索引或同义词映射都将变为不可用。 稍后还原密钥保管库访问权限将还原 index\synonym 映射访问。 有关详细信息，请参阅[保护对密钥保管库的访问](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

1. [登录到 Azure 门户](https://portal.azure.com)并打开 key vault 概述页。 

1. 从左侧导航窗格中选择 "**访问策略**" 设置，然后单击 " **+ 添加新**"。

   ![添加新的 key vault 访问策略](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "添加新的 key vault 访问策略")

1. 单击 "**选择主体**" 并选择 Azure 认知搜索服务。 你可以按名称或按在启用托管标识后显示的对象 ID 进行搜索。

   ![选择密钥保管库访问策略主体](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "选择密钥保管库访问策略主体")

1. 单击 "**密钥权限**"，然后选择 "*获取*"、"*解包密钥*和*环绕键*"。 可以使用 " *Azure Data Lake Storage" 或 "Azure 存储*" 模板快速选择所需的权限。

   必须为 Azure 认知搜索授予以下[访问权限](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)：

   * *Get* -允许搜索服务检索 Key Vault 中密钥的公共部分
   * "*自动换行*"-允许搜索服务使用你的密钥来保护内部加密密钥
   * *解包密钥*-允许搜索服务使用你的密钥来解包内部加密密钥

   ![选择密钥保管库访问策略密钥权限](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "选择密钥保管库访问策略密钥权限")

1. 单击 **"确定"** 并**保存**访问策略更改。

> [!Important]
> Azure 认知搜索中的加密内容配置为使用特定**版本**的特定 Azure Key Vault 密钥。 如果更改了键或版本，则必须更新索引或同义词映射，才能在删除以前的 key\version.**之前**使用新的 key\version 如果无法这样做，将无法使用索引或同义词映射，此时将无法在密钥访问丢失后解密内容。   

## <a name="5---encrypt-content"></a>5-加密内容

使用 Azure 门户无法创建使用客户管理的密钥加密的索引或同义词映射。 使用 Azure 认知搜索 REST API 创建此类索引或同义词映射。

索引和同义词映射都支持用于指定密钥的新顶级**encryptionKey**属性。 

使用 key vault **Uri**、**密钥名称**和密钥保管库密钥的**密钥版本**，可以创建**encryptionKey**定义：

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
> 不会将这些密钥保管库详细信息视为机密，可通过浏览到 Azure 门户中的相关 Azure Key Vault 密钥页面来轻松检索。

如果使用 AAD 应用程序进行 Key Vault 身份验证而不是使用托管标识，请将 AAD 应用程序**访问凭据**添加到加密密钥： 
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
可在[创建索引（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)中找到通过 REST API 创建新索引的详细信息，这里唯一的区别在于，在索引定义中指定加密密钥详细信息： 

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
现在可以发送索引创建请求，然后以正常方式开始使用索引。

## <a name="example-synonym-map-encryption"></a>示例：同义词映射加密

可以在[Create 同义词映射（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)中找到通过 REST API 创建新的同义词映射的详细信息，这里唯一的区别在于，将加密密钥详细信息指定为同义词映射定义的一部分： 

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
你现在可以发送同义词映射创建请求，然后开始正常使用它。

>[!Important] 
> 当无法将**encryptionKey**添加到现有的 Azure 认知搜索索引或同义词映射时，可以通过为三个密钥保管库详细信息中的任何一种提供不同的值（例如，更新密钥版本）来更新它。 更改为新的 Key Vault 项或新的密钥版本时，必须先更新使用该密钥的任何 Azure 认知搜索索引或同义词映射，才能在删除以前的 key\version.**之前**使用新的 key\version 如果无法这样做，将无法使用索引或同义词映射，因为在密钥访问丢失后，它将无法解密内容。   
> 稍后还原密钥保管库访问权限将还原内容访问。

## <a name="aad-app"></a>高级：使用外部托管的 Azure Active Directory 应用程序

当无法使用托管标识时，可以创建一个 Azure Active Directory 应用程序，其中包含 Azure 认知搜索服务的安全主体。 具体而言，在以下情况下，托管标识是不可行的：

* 不能直接向密钥保管库授予搜索服务访问权限（例如，搜索服务与 Azure Key Vault 不同 Active Directory 租户）。

* 需要单个搜索服务来承载多个加密的 indexes\synonym 映射，每个映射使用不同密钥保管库中的不同密钥，其中每个密钥保管库必须使用**不同**的身份进行身份验证。 如果不需要使用其他标识来管理不同的密钥保管库，请考虑使用上面的托管标识选项。  

为了适应此类拓扑，Azure 认知搜索支持使用 Azure Active Directory （AAD）应用程序在搜索服务和 Key Vault 之间进行身份验证。    
若要在门户中创建 AAD 应用程序：

1. [创建 Azure Active Directory 应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [获取应用程序 ID 和身份验证密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，因为创建加密索引需要这些密钥。 需要提供的值包括**应用程序 ID**和**身份验证密钥**。

>[!Important]
> 在决定使用身份验证的 AAD 应用程序而不是托管标识时，请考虑以下事实： Azure 认知搜索无权代表您管理 AAD 应用程序，并由您负责管理 AAD 应用程序，如定期应用程序身份验证密钥的旋转。
> 更改 AAD 应用程序或其身份验证密钥时，必须先更新使用该应用程序的任何 Azure 认知搜索索引或同义词映射，才能在删除以前的应用程序或其授权**密钥之前和**在撤消对其的 Key Vault 访问之前使用新的应用程序 ID\key。
> 如果无法这样做，将无法使用索引或同义词映射，因为在密钥访问丢失后，它将无法解密内容。   

## <a name="next-steps"></a>后续步骤

如果不熟悉 Azure 安全体系结构，请查看[Azure 安全文档](https://docs.microsoft.com/azure/security/)，尤其是本文：

> [!div class="nextstepaction"]
> [静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
