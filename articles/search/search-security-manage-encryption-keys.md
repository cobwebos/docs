---
title: Azure Key Vault 中使用客户托管密钥的静态加密（预览版）- Azure 搜索
description: 通过在 Azure Key Vault 中创建和管理的密钥，来补充 Azure 搜索中基于索引和同义词映射的服务器端加密。
author: NatiNimni
manager: nitinme
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: ce7a8af1416664a3a94b248c95203c8e775e805c
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182409"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault 中使用客户托管密钥的 Azure 搜索加密

> [!Note]
> 使用客户托管密钥的加密目前以预览版提供，请不要在生产环境中使用。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 也可以使用 .NET SDK 版本 8.0-preview。
>
> 此功能不适用于免费服务。 必须使用在 2019 年 1 月 1 日或之后创建的可计费搜索服务。 目前不提供 Azure 门户支持。

默认情况下，Azure 搜索使用[服务托管的密钥](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)静态加密用户内容。 可以使用在 Azure Key Vault 中创建和管理的密钥，通过一个附加的加密层来补充默认加密。 本文将会讲解这些步骤。

通过与 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 的集成来支持服务器端加密。 你可以创建自己的加密密钥并将其存储在 Key Vault 中，或使用 Azure Key Vault 的 API 来生成加密密钥。 使用 Azure Key Vault 还可以审核密钥用法。 

使用客户托管密钥的加密级别是创建这些对象时在索引或同义词映射级别配置的，而不是在搜索服务级别配置的。 无法加密已存在的内容。 

可以使用不同 Key Vault 中的不同密钥。 这意味着，单个搜索服务可以托管多个已加密的索引/同义词映射（可能已使用不同的客户托管密钥加密每个索引/同义词映射），以及未使用客户托管密钥加密的索引/同义词映射。 

## <a name="prerequisites"></a>先决条件

本示例使用以下服务： 

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本教程中使用免费服务。

+ [创建 Azure Key Vault 资源](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)，或者在订阅下找到一个现有的保管库。

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 用于配置任务。

+ [Postman](search-get-started-postman.md)、[Azure PowerShell](search-create-index-rest-api.md) 和 [Azure 搜索 SDK](https://aka.ms/search-sdk-preview) 可用于调用预览版 REST API。 客户托管的加密目前不支持门户或 .NET SDK。

## <a name="1---enable-key-recovery"></a>1 - 启用密钥恢复

此步骤是可选的，但强烈建议执行。 创建 Azure Key Vault 资源后，执行以下 PowerShell 或 Azure CLI 命令，在所选 Key Vault 中启用“软删除”和“清除保护”：   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> 由于客户托管密钥功能的加密本质，删除 Azure Key Vault 密钥后，Azure 搜索将无法检索你的数据。 为了防止意外删除 Key Vault 密钥造成数据丢失，我们强烈建议在所选 Key Vault 中启用“软删除”和“清除保护”。 有关详细信息，请参阅 [Azure Key Vault 软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。   

## <a name="2---create-a-new-key"></a>2 - 创建新密钥

如果使用现有密钥来加密 Azure 搜索内容，请跳过此步骤。

1. [登录到 Azure 门户](https://portal.azure.com)并导航到 Key Vault 仪表板。

1. 在左侧导航窗格中选择“密钥”设置，然后单击“+ 生成/导入”。

1. 在“创建密钥”窗格中，从“选项”列表中，选择要用于创建密钥的方法。 可以**生成**新密钥、**上传**现有密钥，或使用“备份还原”选择密钥的备份。

1. 输入密钥的**名称**，并根据需要选择其他密钥属性。

1. 单击“创建”按钮开始部署。

请记下密钥标识符 – 它由**密钥值 URI**、**密钥名称**和**密钥版本**构成。 稍后需要使用这些信息来定义 Azure 搜索中已加密的索引。
 
![创建新的 Key Vault 密钥](./media/search-manage-encryption-keys/create-new-key-vault-key.png "创建新的 Key Vault 密钥")

## <a name="3---create-a-service-identity"></a>3 - 创建服务标识

将标识分配给搜索服务可以授予 Key Vault 对搜索服务的访问权限。 搜索服务将使用其标识对 Azure Key Vault 进行身份验证。

Azure 搜索支持通过两种方式来分配标识：托管标识，或外部托管的 Azure Active Directory 应用程序。 

如果可能，请使用托管标识。 它是将标识分配给搜索服务的最简单方式，应该可在大多数方案中使用。 如果你对索引和同义词映射使用多个密钥，或者解决方案位于不符合基于标识的身份验证条件的分布式体系结构中，请使用本文末尾所述的高级[外部托管 Azure Active Directory 方法](#aad-app)。

 一般情况下，搜索服务可以使用托管标识对 Azure Key Vault 进行身份验证，而无需在代码中存储凭据。 此类托管标识的生命周期与只包含一个托管标识的搜索服务的生命周期密切相关。 [了解有关托管标识的详细信息](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

1. 若要创建托管标识，请[登录到 Azure 门户](https://portal.azure.com)并打开搜索服务仪表板。 

1. 在左侧导航窗格中单击“标识”，将其状态更改为“开”，然后单击“保存”。

![启用托管标识](./media/search-enable-msi/enable-identity-portal.png "启用托管标识")

## <a name="4---grant-key-access-permissions"></a>4 - 授予密钥访问权限

若要使搜索服务能够使用 Key Vault 密钥，需要向搜索服务授予特定的访问权限。

可在任意给定时间撤销访问权限。 撤销后，使用该 Key Vault 的任何搜索服务索引或同义词映射都将不可用。 以后还原 Key Vault 访问权限会还原索引/同义词映射访问权限。 有关详细信息，请参阅[保护对 Key Vault 的访问](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。

1. [登录到 Azure 门户](https://portal.azure.com)并打开 Key Vault 概述页。 

1. 在左侧导航窗格中选择“访问策略”设置，然后单击“+ 新增”。

   ![添加新的 Key Vault 访问策略](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "添加新的 Key Vault 访问策略")

1. 单击“选择主体”并选择你的 Azure 搜索服务。 可以根据启用托管标识后显示的名称或对象 ID 来搜索该搜索服务。

   ![选择 Key Vault 访问策略主体](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "选择 Key Vault 访问策略主体")

1. 单击“密钥权限”，然后选择“获取”、“解包密钥”和“包装密钥”。 可以使用 Azure Data Lake Storage 或 Azure 存储模板快速选择所需的权限。

   必须向 Azure 搜索授予以下[访问权限](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)：

   * 获取 - 可让搜索服务检索 Key Vault 中密钥的公共部分
   * 包装密钥 - 可让搜索服务使用密钥来保护内部加密密钥
   * 解包密钥 - 可让搜索服务使用密钥来解包内部加密密钥

   ![选择 Key Vault 访问策略密钥权限](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "选择 Key Vault 访问策略密钥权限")

1. 单击“确定”，然后**保存**访问策略更改。

> [!Important]
> Azure 搜索中已加密的内容配置为使用特定**版本**的特定 Azure Key Vault 密钥。 如果你更改密钥或版本，必须先将索引或同义词映射更新为使用新的密钥/版本，**然后**删除以前的密钥/版本。 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。   

## <a name="5---encrypt-content"></a>5 - 加密内容

目前无法在 Azure 门户中创建使用客户托管密钥加密的索引或同义词映射。 请使用 Azure 搜索 REST API 来创建此类索引或同义词映射。

索引和同义词映射都支持一个新的用于指定密钥的顶级 **encryptionKey** 属性。 

使用 Key Vault 密钥的 **Key Vault URI**、**密钥名称**和**密钥版本**可以创建 **encryptionKey** 定义：

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

## <a name="example-index-encryption"></a>例如：索引加密
[创建索引（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-index)中提供了有关通过 REST API 创建新索引的详细信息。本文的唯一差别在于，需要将加密密钥详细信息指定为索引定义的一部分： 

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

## <a name="example-synonym-map-encryption"></a>例如：同义词映射加密

[创建同义词映射（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)中提供了有关通过 REST API 创建新同义词映射的详细信息。本文的唯一差别在于，需要将加密密钥详细信息指定为同义词映射定义的一部分： 

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
> 尽管无法将 **encryptionKey** 添加到现有的 Azure 搜索索引或同义词映射，但可以通过为三项 Key Vault 详细信息的任何一项提供不同的值，来更新该属性（例如，更新密钥版本）。 更改为新的 Key Vault 密钥或新的密钥版本时，必须先将使用该密钥的任何 Azure 搜索索引或同义词映射更新为使用新的密钥/版本，**然后**删除以前的密钥/版本。 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。   
> 以后还原 Key Vault 访问权限会还原内容访问权限。

## <a name="aad-app"></a>高级：使用外部托管的 Azure Active Directory 应用程序

如果无法使用托管标识，可以使用 Azure 搜索服务的安全主体创建一个 Azure Active Directory 应用程序。 具体而言，在以下情况下无法使用托管标识：

* 无法直接授予搜索服务对 Key Vault 的访问权限（例如，如果搜索服务与 Azure Key Vault 位于不同的 Active Directory 租户中）。

* 必须使用单个搜索服务来托管多个已加密的索引/同义词映射，其中每个索引/同义词映射使用不同 Key Vault 中的不同密钥，而每个 Key Vault 必须使用**不同的标识**进行身份验证。 如果不要求使用不同的标识来管理不同的 Key Vault，请考虑使用上述托管标识选项。  

为了适应这种拓扑，Azure 搜索支持使用 Azure Active Directory (AAD) 应用程序在搜索服务与 Key Vault 之间进行身份验证。    
若要在门户中创建 AAD 应用程序：

1. [创建 Azure Active Directory 应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

1. [获取应用程序 ID 和身份验证密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，因为创建加密的索引时需要用到这些信息。 需要提供的值包括**应用程序 ID** 和**身份验证密钥**。

>[!Important]
> 决定使用 AAD 应用程序而不是托管标识进行身份验证时，请考虑到以下事实：Azure 搜索无权代表你管理 AAD 应用程序，需要由你自己管理 AAD 应用程序，例如，定期轮换应用程序身份验证密钥。
> 更改 AAD 应用程序或其身份验证密钥时，必须先将使用该应用程序的任何 Azure 搜索索引或同义词映射更新为使用新的应用程序 ID/密钥，**然后**删除以前的应用程序或其授权密钥，然后再撤消 Key Vault 对它的访问权限。
> 否则会使该索引或同义词映射变得不可用，因为在失去密钥访问权限后无法解密内容。   

## <a name="next-steps"></a>后续步骤

如果你不熟悉 Azure 安全体系结构，请查看 [Azure 安全文档](https://docs.microsoft.com/azure/security/)，具体而言，是以下文章：

> [!div class="nextstepaction"]
> [静态数据加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
