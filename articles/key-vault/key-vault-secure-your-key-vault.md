---
title: 保护 Azure 密钥保管库 - Azure 密钥保管库 | Microsoft Docs
description: 管理 Azure Key Vault、密钥和机密的访问权限。 介绍 Key Vault 的身份验证和授权模型以及如何保护 Key Vault。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 8a0300eeda49d85ffc08db8f285550e217613dcf
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821606"
---
# <a name="secure-your-key-vault"></a>保护密钥保管库

Azure Key Vault 是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 因为此数据是敏感数据和业务关键数据，所以必须保护对 Key Vault 的访问，只允许得到授权的应用程序和用户进行访问。 本文简要介绍了 Key Vault 访问模型。 介绍了身份验证和授权，并说明了如何保护访问。

## <a name="overview"></a>概述

可通过以下两个独立接口来控制对密钥保管库的访问：管理平面和数据平面。 
**管理平面**处理管理保管库，例如，创建保管库、更新保管库和删除保管库。 
**数据平面**处理保管库内的机密，即创建、更新、删除和读取保管库内的机密。 对于这两个平面，在调用方（用户或应用程序）可以访问 Key Vault 前，需要进行适当的身份验证和授权。 身份验证可确认调用方的身份，授权确定允许调用方执行的操作。

管理平面和数据平面都使用 Azure Active Directory 进行身份验证。 对于授权，管理平面使用基于角色的访问控制 (RBAC)，而数据平面使用密钥保管库访问策略。

下面是相关主题的简要概述：

使用 Azure Active Directory 进行身份验证 - 此部分介绍调用方如何通过管理平面和数据平面使用 Azure Active Directory 进行身份验证，以便访问密钥保管库。 

对于身份验证，这两个平面都使用 Azure Active Directory (Azure AD)。 对于授权，管理平面使用基于角色的访问控制 (RBAC)，而数据平面使用 Key Vault 访问策略。

## <a name="authenticate-by-using-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证

在 Azure 订阅中创建 Key Vault 时，该 Key Vault 自动与订阅的 Azure AD 租户关联。 所有调用方都必须在此租户中注册并通过身份验证，才能访问该 Key Vault。 此项要求适用于管理平面访问和数据平面访问。 在这两种情况下，应用程序可以以两种方式访问 Key Vault：

* **用户+应用访问**：适用于代表已登录用户访问 Key Vault 的应用程序。 此类访问的示例有 Azure PowerShell 和 Azure 门户。 可通过两种方法向用户授予访问权限：

  - 从任何应用程序访问 Key Vault。
  - 仅在他们使用特定应用程序时才访问 Key Vault（称为复合标识）。

* **仅限应用访问**：适用于作为守护程序服务或后台作业运行的应用程序。 向应用程序的标识授予访问密钥保管库的权限。

在这两种类型的应用程序中，应用程序均通过 Azure AD 使用任一[支持的身份验证方法](../active-directory/develop/authentication-scenarios.md)进行身份验证，并获取令牌。 所使用的身份验证方法取决于应用程序类型。 然后，应用程序会使用此令牌，并将 REST API 请求发送到 Key Vault。 管理平面请求通过 Azure 资源管理器终结点路由。 如果访问的是数据平面，则应用程序直接与 Key Vault 终结点交流。 有关详细信息，请参阅[整个身份验证流](../active-directory/develop/v1-protocols-oauth-code.md)。 

应用程序请求令牌的资源名取决于应用程序所访问的平面。 资源名是管理平面终结点或数据平面终结点，具体取决于 Azure 环境。 有关详细信息，请参阅本文后面的表。

对这两种平面进行身份验证使用单一机制具有一些优点：

* 组织可以集中控制其组织中的对所有 Key Vault 的访问。
* 如果某个用户离开，则他们将立即失去对组织中所有 Key Vault 的访问权限。
* 组织可以通过 Azure AD 中的选项自定义身份验证（例如，启用多重身份验证以提高安全性）。

## <a name="the-management-plane-and-the-data-plane"></a>管理平面和数据平面

使用管理平面来管理 Key Vault 本身。 这包括管理属性以及设置数据平面访问策略等操作。 使用数据平面添加、删除、修改和使用存储在 Key Vault 中的密钥、机密和证书。

通过下表中列出的不同终结点来访问管理平面和数据平面接口。 表中的第二列描述了不同 Azure 环境中这些终结点的 DNS 名称。 第三列描述可在每个访问平面中执行的操作。 每个访问平面还有自身的访问控制机制。 管理平面访问控制是使用 Azure 资源管理器基于角色的访问控制 (RBAC) 设置的。 数据平面访问控制是使用 Key Vault 访问策略设置的。

| 访问平面 | 访问终结点 | 操作 | 访问控制机制 |
| --- | --- | --- | --- |
| 管理平面 |**全球：**<br> management.azure.com:443<br><br> **Azure 中国世纪互联：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> management.microsoftazure.de:443 |创建/读取/更新/删除 Key Vault <br> 设置 Key Vault 的访问策略<br>设置 Key Vault 的标记 |Azure 资源管理器 RBAC |
| 数据平面 |**全球：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 中国世纪互联：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government：**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |对于密钥：解密、加密、UnwrapKey、WrapKey、验证、签名、获取、列出、更新、创建、导入、删除、备份、还原<br><br> 对于机密：获取、列出、设置、删除 |Key Vault 访问策略 |

管理平面访问控制与数据平面访问控制相互独立。 例如，若要授权应用程序使用 Key Vault 中的密钥，只需授予数据平面访问权限。 通过 Key Vault 访问策略授予访问权限。 相反，若用户需要读取 Key Vault 属性和标记而不需要访问数据（密钥、机密或证书），则只需管理平面访问权限。 通过使用 RBAC 向用户授予读取权限来授予权限。

## <a name="management-plane-access-control"></a>管理平面访问控制

管理平面由影响 Key Vault 本身的操作构成，例如：

- 创建或删除 Key Vault 保管库。
- 获取订阅中保管库的列表。
- 检索 Key Vault 属性（例如 SKU 和标记）。
- 设置 Key Vault 访问策略，控制用户和应用程序对密钥和机密的访问权限。

管理平面访问控制使用 RBAC。  

### <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

每个 Azure 订阅都具有 Azure AD 实例。 向来自该目录的用户、组和应用程序授予访问权限，以便在使用 Azure 资源管理器部署模型的 Azure 订阅中管理资源。 此类型的访问控制称为 RBAC。 若要管理此访问权限，可以使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI 工具](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

在资源组中创建 Key Vault，并使用 Azure AD 控制对管理平面的访问权限。 例如，可以授予用户或组管理资源组中 Key Vault 的权限。

可以通过分配相应的 RBAC 角色，向用户、组和应用程序授予适用于特定范围的访问权限。 例如，要向用户授予管理 Key Vault 的访问权限，可将预定义的“Key Vault 参与者”角色分配给位于特定范围内的此用户。 在此情况下，该范围可以是订阅、资源组，或特定的 Key Vault。 在订阅级别分配的角色适用于该订阅中的所有资源组和资源。 在资源组级别分配的角色适用于该资源组中的所有资源。 为特定资源分配的角色适用于该资源。 有多种预定义角色（参阅 [RBAC：内置角色](../role-based-access-control/built-in-roles.md)）。 如果预定义角色不符合需求，可以定义自己的角色。

> [!IMPORTANT]
> 请注意，如果用户具有 Key Vault 管理平面的参与者权限，则该用户可以通过设置 Key Vault 访问策略来授予自己对数据平面的访问权限。 因此，应严格控制对 Key Vault 具有参与者访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。
>

## <a name="data-plane-access-control"></a>数据平面访问控制

Key Vault 数据平面操作应用于存储的对象，例如密钥、机密和证书。 密钥操作包括创建、导入、更新、列出、备份和还原密钥。 加密操作包括签名、验证、加密、解密、包装、解包、设置标记和设置密钥的其他属性。 同样，针对机密的操作包括获取、设置、列出和删除。

通过设置 Key Vault 的访问策略授予数据平面访问权限。 用户、组或应用程序必须具有 Key Vault 的管理平面的参与者权限，才能设置该 Key Vault 的访问策略。 可以向用户、组或应用程序授予对 Key Vault 中的密钥或机密执行特定操作的访问权限。 Key Vault 最多支持 1024 个 Key Vault 访问策略条目。 若要向多个用户授予对数据平面的访问权限，创建一个 Azure AD 安全组，并将用户添加到该组。

### <a name="key-vault-access-policies"></a>Key Vault 访问策略

Key Vault 访问策略单独授予对密钥、机密和证书的权限。 例如，可以向用户授予仅限密钥的访问权限，而不授予对机密的权限。 访问密钥、机密或证书的权限是保管库级别的。 Key Vault 访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI 工具](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs) 或 [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx) 设置 Key Vault 的访问策略。

> [!IMPORTANT]
> Key Vault 访问策略适用于保管库级别。 例如，授予某个用户创建和删除密钥的权限时，该用户可以针对该密钥保管库中的所有密钥执行这些操作。

除了使用访问策略以外，还可以通过使用[适用于 Azure Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)来限制数据平面访问权限。 配置[防火墙和虚拟网络规则](key-vault-network-security.md)以提供额外的安全层。

## <a name="example"></a>示例

假设正在开发一个应用程序，该应用程序使用 SSL 证书，使用 Azure 存储进行数据存储，且使用 RSA 2048 位密钥进行签名操作。 假设此应用程序在 Azure 虚拟机（或虚拟机规模集）中运行。 可以使用 Key Vault 存储所有应用程序机密，并存储供应用程序用来通过 Azure AD 进行身份验证的启动证书。

下面总结了存储的密钥和机密类型：

* **SSL 证书**：用于 SSL。
* **存储密钥**：用于获取存储帐户的访问权限。
* **RSA 2048 位密钥**：用于签名操作。
* **启动证书**：用于使用 Azure AD 进行身份验证。 授予访问权限后，可以提取存储密钥并使用 RSA 密钥进行签名。

现在，让我们来了解管理、部署和审核此应用程序的人员。 本示例使用了三个角色。

* **安全团队**：通常是 CSO（首席安全官）办公室中的 IT 人员或同等职务的人员。 此团队负责机密的适当保管。 示例包括 SSL 证书、用于签名的 RSA 密钥、连接字符串和存储帐户密钥。
* **开发人员/操作员**：开发应用程序然后将其部署到 Azure 的人员。 通常，他们不是安全团队的成员，因此不应有权访问 SSL 证书和 RSA 密钥等敏感数据。 他们部署的应用程序才应有权访问这些数据。
* **审核员**：通常是独立于开发人员和常规 IT 人员的一组不同的人员。 他们的职责是评审证书、密钥和机密的使用和维护，确保遵从安全标准。 

在此应用程序的范围之外还有一个角色，它与本文所述的内容相关。 该角色是订阅（或资源组）管理员。 订阅管理员设置安全团队的初始访问权限。 订阅管理员使用包含此应用所需资源的资源组来向安全团队授予访问权限。

现在，我们看看在此应用程序的上下文中，每个角色可执行哪些操作。

* **安全团队**
  * 创建 Key Vault。
  * 打开 Key Vault 日志记录。
  * 添加密钥/机密。
  * 创建密钥备份以进行灾难恢复。
  * 设置 Key Vault 访问策略，以向用户和应用程序授予执行特定操作的权限。
  * 定期滚动密钥/机密。
* **开发人员/操作人员**
  * 从安全团队获取启动证书和 SSL 证书引用（指纹）、存储密钥（机密 URI）以及签名密钥（密钥 URI）。
  * 开发和部署以编程方式访问密钥和机密的应用程序。
* **审核人员**
  * 检查使用情况日志，以确认正确使用了密钥/机密且符合数据安全标准。

现在，让我们看看每个角色和应用程序需要哪些访问权限才能执行其分配的任务。 

| 用户角色 | 管理平面权限 | 数据平面权限 |
| --- | --- | --- |
| 安全团队 |密钥保管库参与者 |密钥：备份、创建、删除、获取、导入、列出、还原 <br> 机密：所有 |
| 开发人员/操作员 |Key Vault 部署权限，以便他们部署的虚拟机可以从 Key Vault 中提取机密。 |无 |
| 审核人员 |无 |密钥：列出<br>机密：列出 |
| 应用程序 |无 |密钥：签名<br>机密：获取 |

> [!NOTE]
> 审核员需要密钥和机密的列出权限，以便他们可以检查未在日志中发出的密钥和机密的属性。 这些属性包括标记、激活和到期日期。
> 
> 

除了 Key Vault 权限外，这三个角色都还需要其他资源的访问权限。 例如，为了能够部署 VM（或 Azure 应用服务的 Web 应用功能），开发人员和操作员还需要具有对这些资源类型的参与者访问权限。 审核员需要存储 Key Vault 日志的存储帐户的“读取”权限。

由于本文的重点是保护对 Key Vault 的访问权限，因此仅对与该主题相关的概念进行说明。 有关部署证书、以编程方式访问密钥和机密的详细信息位于其他位置。 例如：

- [将证书从客户托管的 Key Vault 部署到 VM](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)（博客文章）中介绍了如何将存储在 Key Vault 中的证书部署到 VM。
- [Azure Key Vault 客户端示例下载](https://www.microsoft.com/download/details.aspx?id=45343)演示了如何使用启动证书向 Azure AD 进行身份验证以访问 Key Vault。

可以通过使用 Azure 门户授予大部分访问权限。 若要授予粒度权限，则可能需要使用 Azure PowerShell 或 CLI 来实现期望的结果。 

以下 PowerShell 代码片段假设：

* Azure AD 管理员已创建代表这三个角色的安全组，即 Contoso 安全团队、Contoso 应用开发人员和 Contoso 应用审核员。 管理员也已经向他们所属的组添加了用户。
* **ContosoAppRG** 是所有资源所在的资源组。 **contosologstorage** 是日志的存储位置。 
* Key Vault ContosoKeyVault 和用于 Key Vault 日志的存储帐户 contosologstorage 必须位于相同的 Azure 位置。

首先，订阅管理员向安全团队分配 `key vault Contributor` 和 `User Access Administrator` 角色。 这些角色使安全团队可管理对其他资源的访问权限以及资源组 ContosoAppRG 中的 Key Vault。

```PowerShell
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

以下脚本演示安全团队如何创建 Key Vault 并设置日志记录和访问权限。 有关 Key Vault 访问策略权限的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](about-keys-secrets-and-certificates.md)。

```PowerShell
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

仅可将定义的自定义角色分配给其中创建了 `ContosoAppRG` 资源组的订阅。 如果将相同的自定义角色用于其他订阅中的其他项目，则可在其范围内添加更多订阅。

具有“部署/操作”权限的开发人员和操作员的自定义角色分配的范围限定为资源组。 这仅允许在资源组 `ContosoAppRG` 中创建的 VM 获取访问机密（SSL 证书和启动证书）的权限。 开发人员和操作员团队成员在另一个资源组中创建的 VM 将无权访问这些机密，即使拥有机密 URI 也是如此。

此示例展示了一个简单的场景。 现实场景可能更复杂，可以根据需求调整针对 Key Vault 的权限。 在本示例中，假设安全团队将提供开发人员和操作员需要在其应用程序中引用的密钥和机密（URI 和指纹）。 开发人员和操作员不需要任何数据平面访问权限。 本示例重点介绍对密钥保管库的保护。 对于保护 [VM](https://azure.microsoft.com/services/virtual-machines/security/)、[存储帐户](../storage/common/storage-security-guide.md)和其他 Azure 资源，应进行类似的考虑。

> [!NOTE]
> 此示例介绍如何在生产中锁定 Key Vault 访问。 开发人员应具有其自己的订阅或资源组，他们具有这些资源组的完整权限，以管理其用来开发应用程序的保管库、VM 和存储帐户。

我们强烈建议[配置 Key Vault 防火墙和虚拟网络](key-vault-network-security.md)，以进一步保护对 Key Vault 的访问。

## <a name="resources"></a>资源

* [Azure Active Directory 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC：内置角色](../role-based-access-control/built-in-roles.md)
  
* [了解 Resource Manager 部署和经典部署](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [使用 Azure PowerShell 管理基于角色的访问控制](../role-based-access-control/role-assignments-powershell.md)
  
* [使用 REST API 管理基于角色的访问控制](../role-based-access-control/role-assignments-rest.md)
  
* [Ignite 中提供的适用于 Microsoft Azure 的基于角色的访问控制](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  此 2015 年 Microsoft Ignite 大会视频讨论了 Azure 中的访问管理和报告功能。 它还介绍了使用 Azure AD 保护对 Azure 订阅的访问权限的最佳做法。
* [使用 OAuth 2.0 和 Azure AD 来授权访问 Web 应用程序](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  此文档是 REST API 以编程方式管理 Key Vault 的参考，包括设置 Key Vault 访问策略。
* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Key access control（密钥访问控制）](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Secret access control（机密访问控制）](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* 使用 PowerShell [设置](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy)和[删除](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) Key Vault 访问策略
  
## <a name="next-steps"></a>后续步骤

[配置密钥保管库防火墙和虚拟网络](key-vault-network-security.md)

有关面向管理员的入门教程，请参阅 [Azure 密钥保管库入门](key-vault-get-started.md)。

有关 Key Vault 使用情况日志记录的详细信息，请参阅 [Azure Key Vault 日志记录](key-vault-logging.md)。

有关将密钥和机密与 Azure Key Vault 配合使用的详细信息，请参阅[关于密钥和机密](https://msdn.microsoft.com/library/azure/dn903623.aspx)。

如果对 Key Vault 有任何疑问，请访问[论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。

