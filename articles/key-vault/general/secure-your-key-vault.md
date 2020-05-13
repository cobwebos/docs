---
title: 保护对密钥保管库的访问 - Azure 密钥保管库 | Microsoft Docs
description: 管理 Azure Key Vault、密钥和机密的访问权限。 介绍 Key Vault 的身份验证和授权模型以及如何保护 Key Vault。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 6a838455bfda47dee55e8726e5eab071d2dfe4f7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121144"
---
# <a name="secure-access-to-a-key-vault"></a>保护对密钥保管库的访问

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 因为此数据是敏感数据和业务关键数据，所以需要保护对密钥保管库的访问，只允许得到授权的应用程序和用户进行访问。 本文简要介绍了 Key Vault 访问模型。 其中介绍了身份验证和授权，以及如何保护对密钥保管库的访问。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>访问模型概述

可通过以下两个接口来控制对密钥保管库的访问：**管理平面**和**数据平面**。 管理平面用于管理密钥保管库本身。 此平面中的操作包括创建和删除密钥保管库、检索密钥保管库属性以及更新访问策略。 数据平面用于处理密钥保管库中存储的数据。 可以添加、删除和修改密钥、机密及证书。

若要在任一平面中访问密钥保管库，所有调用方（用户或应用程序）都必须进行适当的身份验证并拥有适当的授权。 身份验证可确定调用方的身份。 授权可确定调用方能够执行的操作。

对于身份验证，这两个平面都使用 Azure Active Directory (Azure AD)。 对于授权，管理平面使用基于角色的访问控制 (RBAC)，而数据平面使用密钥保管库访问策略。

## <a name="active-directory-authentication"></a>Active Directory 身份验证

在 Azure 订阅中创建密钥保管库时，该密钥保管库自动与订阅的 Azure AD 租户关联。 两个平面中的所有调用方都必须在此租户中注册并进行身份验证，然后才能访问该密钥保管库。 在这两种情况下，应用程序可以通过两种方式访问密钥保管库：

- **用户加应用程序访问**：应用程序代表已登录的用户访问密钥保管库。 此类访问的示例包括 Azure PowerShell 和 Azure 门户。 用户访问权限通过两种方式授予。 用户可以从任何应用程序访问密钥保管库，或者用户必须使用特定的应用程序（称为_复合标识_）。
- **仅限应用程序的访问**：应用程序作为守护程序服务或后台作业运行。 向应用程序标识授予访问密钥保管库的权限。

对于这两种类型的访问，应用程序都使用 Azure AD 进行身份验证。 应用程序根据应用程序类型使用任何[支持的身份验证方法](../../active-directory/develop/authentication-scenarios.md)。 应用程序通过获取平面中资源的令牌来授予访问权限。 资源是管理平面或数据平面中基于 Azure 环境的终结点。 应用程序使用令牌并向密钥保管库发送 REST API 请求。 若要了解详细信息，请查看[整个身份验证流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

对这两种平面使用单一身份验证机制模型具有多个优点：

- 组织可以集中控制对其组织中的所有密钥保管库的访问。
- 离职的用户会立即失去对组织中所有密钥保管库的访问权限。
- 组织可以通过 Azure AD 中的选项自定义身份验证（例如，启用多重身份验证以提高安全性）。

## <a name="resource-endpoints"></a>资源终结点

应用程序通过终结点访问平面。 两个平面的访问控制独立运行。 若要授权应用程序使用密钥保管库中的密钥，可以使用密钥保管库访问策略授予数据平面访问权限。 若要授予用户对密钥保管库属性和标记的读取访问权限，但不授予对数据（密钥、机密或证书）的访问权限，请使用 RBAC 授予管理平面访问权限。

下表显示了用于管理平面和数据平面的终结点。

| 访问&nbsp;平面 | 访问终结点 | 操作 | 访问&nbsp;控制机制 |
| --- | --- | --- | --- |
| 管理平面 | **全球：**<br> management.azure.com:443<br><br> **Azure 中国世纪互联：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> management.microsoftazure.de:443 | 创建、读取、更新和删除密钥保管库<br><br>设置密钥保管库访问策略<br><br>设置密钥保管库标记 | Azure 资源管理器 RBAC |
| 数据平面 | **全球：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 中国世纪互联：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government：**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 密钥：解密、加密，<br> 解包、包装、验证、签名，<br> 获取、列出、更新、创建，<br> 导入、删除、备份、还原<br><br> 机密：获取、列出、设置、删除 | Key Vault 访问策略 |

## <a name="management-plane-and-rbac"></a>管理平面和 RBAC

在管理平面中，使用 RBAC（基于角色的访问控制）对调用方可以执行的操作进行授权。 在 RBAC 模型中，每个 Azure 订阅都有一个 Azure AD 实例。 可以从此目录向用户、组和应用程序授予访问权限。 授予访问权限以管理 Azure 订阅中使用 Azure 资源管理器部署模型的资源。 若要授予访问权限，请使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure 资源管理器 REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

可以在资源组中创建密钥保管库，并使用 Azure AD 管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 通过分配适当的 RBAC 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 RBAC 角色：

- **订阅**：在订阅级别分配的 RBAC 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 RBAC 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 RBAC 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [RBAC：内置角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果用户具有密钥保管库管理平面的 `Contributor` 权限，则该用户可以通过设置密钥保管库访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有 `Contributor` 角色访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>数据平面和访问策略

通过为密钥保管库设置密钥保管库访问策略授予数据平面访问权限。 若要设置这些访问策略，用户、组或应用程序必须具有该密钥保管库管理平面的 `Contributor` 权限。

可以向用户、组或应用程序授予对密钥保管库中的密钥或机密执行特定操作的访问权限。 密钥保管库最多支持 1024 个密钥保管库访问策略条目。 若要向多个用户授予对数据平面的访问权限，创建一个 Azure AD 安全组，并将用户添加到该组。

<a id="key-vault-access-policies"></a> 密钥保管库访问策略单独授予对密钥、机密和证书的权限。 可以仅授予用户对密钥的访问权限，而不授予对机密的访问权限。 密钥、机密或证书的访问权限是保管库级别的。 密钥保管库访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 若要为密钥保管库设置访问策略，可以使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI 工具](/cli/azure/install-azure-cli?view=azure-cli-latest)、[PowerShell](/powershell/azureps-cmdlets-docs) 或[密钥保管库管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)。

> [!IMPORTANT]
> Key Vault 访问策略适用于保管库级别。 如果授予某个用户创建和删除密钥的权限，该用户可以针对该密钥保管库中的所有密钥执行这些操作。
>

你可以使用[Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)限制数据平面访问。 可以配置[防火墙和虚拟网络规则](network-security.md)以提供额外的安全层。

## <a name="example"></a>示例

在此示例中，我们将开发一个应用程序，该应用程序使用证书来实现 TLS/SSL、使用 Azure 存储进行数据存储，并使用 RSA 2,048 位密钥进行签名操作。 我们的应用程序在 Azure 虚拟机 (VM)（或虚拟机规模集）中运行。 我们可以使用密钥保管库来存储应用程序机密。 我们可以存储应用程序用于通过 Azure AD 进行身份验证的启动证书。

我们需要对以下存储密钥和机密的访问权限：
- **TLS/SSL 证书**：用于 TLS/SSL。
- **存储密钥**：无法访问存储帐户。
- **RSA 2048 位密钥**：用于签名操作。
- **启动证书**：用于使用 Azure AD 进行身份验证。 授予访问权限后，可以提取存储密钥并使用 RSA 密钥进行签名。

我们需要定义以下角色，以指定可以管理、部署和审核应用程序的用户：
- **安全团队**：CSO（首席安全官）办公室中的 IT 人员或类似参与者。 安全团队负责机密的适当保管。 机密可能包括 TLS/SSL 证书、用于签名的 RSA 密钥、连接字符串和存储帐户密钥。
- **开发人员和操作人员**：开发应用程序并在 Azure 中进行部署的人员。 此团队的成员不属于安全人员。 他们不应有权访问 TLS/SSL 证书和 RSA 密钥等敏感数据。 仅他们部署的应用程序才应有权访问敏感数据。
- **审核员**：此角色适用于不属于开发人员或一般 IT 人员的参与者。 他们评审证书、密钥和机密的使用及维护，确保符合安全标准。

还有一个超出我们应用程序范围的角色：订阅（或资源组）管理员。 订阅管理员为安全团队设置初始访问权限。 他们通过使用具有应用程序所需资源的资源组来授予安全团队访问权限。

我们需要为角色的以下操作进行授权：

**安全团队**
- 创建密钥保管库。
- 打开密钥保管库日志记录。
- 添加密钥和机密。
- 为灾难恢复创建密钥备份。
- 设置密钥保管库访问策略，向用户和应用程序授予执行特定操作的权限。
- 定期滚动密钥和机密。

**开发人员和操作人员**
- 从安全团队获取启动证书和 TLS/SSL 证书（指纹）、存储密钥（机密 URI）以及用于签名的 RSA 密钥（密钥 URI）的引用。
- 以编程方式开发和部署用于访问密钥和机密的应用程序。

**审核人员**
- 查看密钥保管库日志以确认正确使用了密钥和机密且符合数据安全标准。

下表总结了我们的角色和应用程序的访问权限。

| 角色 | 管理平面权限 | 数据平面权限 |
| --- | --- | --- |
| 安全团队 | 密钥保管库参与者 | 密钥：备份、创建、删除、获取、导入、列出、还原<br>机密：所有操作 |
| 开发人员和&nbsp;操作人员 | 密钥保管库部署权限<br><br> **注意**：此权限允许已部署的 VM 从密钥保管库提取机密。 | 无 |
| 审核人员 | 无 | 密钥：列出<br>机密：列出<br><br> **注意**：此权限让审核员能够检查日志中未发出的密钥和机密的属性（标记、激活日期、到期日期）。 |
| 应用程序 | 无 | 密钥：签名<br>机密：获取 |

三个团队角色需要访问其他资源的权限以及密钥保管库权限。 若要部署 VM（或 Azure 应用服务的 Web 应用功能），开发人员和操作人员需要对这些资源类型的 `Contributor` 访问权限。 审核员需要具有对存储密钥保管库日志的存储帐户的“读取”访问权限。

有关如何以编程方式部署证书、访问密钥和机密的详细信息，请参阅以下资源：
- 了解如何[将证书从客户托管的密钥保管库部署到 VM](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)（博客文章）。
- 查看[Azure Key Vault 客户端示例](https://docs.microsoft.com/samples/browse/?term=Key%20Vault)。 此内容介绍了如何使用启动证书对 Azure AD 进行身份验证以访问密钥保管库。

可以通过使用 Azure 门户授予大部分访问权限。 若要授予粒度权限，可以使用 Azure PowerShell 或 Azure CLI。

本部分中的 PowerShell 代码片段基于以下假设生成：
- Azure AD 管理员已创建安全组来表示三个角色：Contoso 安全团队、Contoso 应用开发运营团队和 Contoso 应用审核员。 管理员已将用户添加到其各自的组中。
- 所有资源都位于 **ContosoAppRG** 资源组中。
- 密钥保管库日志存储在 **contosologstorage** 存储帐户中。
- **ContosoKeyVault** 密钥保管库和 **contosologstorage** 存储帐户位于同一 Azure 位置。

订阅管理员将 `key vault Contributor` 和 `User Access Administrator` 角色分配给安全团队。 这些角色使安全团队可管理对其他资源和密钥保管库的访问，且它们都位于 **ContosoAppRG** 资源组中。

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

安全团队创建密钥保管库并设置日志记录和访问权限。

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

定义的自定义角色只能分配给创建 **ContosoAppRG** 资源组所在的订阅。 若要将自定义角色用于其他订阅中的其他项目，请将其他订阅添加到角色的范围。

对于我们的开发运营人员，密钥保管库 `deploy/action` 权限的自定义角色分配范围限定为资源组。 仅允许在 **ContosoAppRG** 资源组中创建的 VM 访问机密（TLS/SSL 和启动证书）。 即使 VM 具有机密 URI，由开发运营成员在其他资源组中创建的 VM 也无法访问这些机密。

我们的示例介绍了一个简单的方案。 现实方案可能更复杂。 可以根据需要调整密钥保管库的权限。 我们假设安全团队提供密钥和机密引用（URI 和指纹），开发运营员工在其应用程序中使用这些引用。 开发人员和操作员不需要任何数据平面访问权限。 我们将重点放在如何保护密钥保管库上。 对于保护 [VM](https://azure.microsoft.com/services/virtual-machines/security/)、[存储帐户](../../storage/blobs/security-recommendations.md)和其他 Azure 资源，应进行类似的考虑。

> [!NOTE]
> 此示例介绍如何在生产中锁定密钥保管库访问。 开发人员应具有其自己的订阅或资源组，他们具有这些订阅或资源组的完整权限，可管理其用来开发应用程序的保管库、VM 和存储帐户。

我们建议[配置密钥保管库防火墙和虚拟网络](network-security.md)，以进一步保护对密钥保管库的访问。

## <a name="resources"></a>资源

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC：内置角色](../../role-based-access-control/built-in-roles.md)

* [了解资源管理器部署和经典部署](../../azure-resource-manager/management/deployment-models.md)

* [使用 Azure PowerShell 管理 RBAC](../../role-based-access-control/role-assignments-powershell.md)

* [使用 REST API 管理 RBAC](../../role-based-access-control/role-assignments-rest.md)

* [适用于 Microsoft Azure 的 RBAC](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    此 2015 年 Microsoft Ignite 大会视频讨论了 Azure 中的访问管理和报告功能。 它还介绍了使用 Azure AD 保护对 Azure 订阅的访问权限的最佳做法。

* [使用 OAuth 2.0 和 Azure AD 来授权访问 Web 应用程序](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    REST API 以编程方式管理密钥保管库的参考，包括设置密钥保管库访问策略。

* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Key access control（密钥访问控制）](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Secret access control（机密访问控制）](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* 使用 PowerShell [设置](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy)和[删除](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy)密钥保管库访问策略。

## <a name="next-steps"></a>后续步骤

配置[密钥保管库防火墙和虚拟网络](network-security.md)。

有关管理员的入门教程，请参阅[什么是 Azure Key Vault？](overview.md)）。

有关 Key Vault 的使用日志记录的详细信息，请参阅[Azure Key Vault 日志记录](logging.md)）。

有关将密钥和机密与 Azure Key Vault 配合使用的详细信息，请参阅[关于密钥和机密](https://msdn.microsoft.com/library/azure/dn903623.aspx)。

如果对 Key Vault 有任何疑问，请访问[论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
