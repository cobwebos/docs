---
title: 分配 Azure Key Vault 访问策略
description: 如何使用 Azure 门户、Azure CLI 或 Azure PowerShell 将 Key Vault 访问策略分配到服务主体或应用程序标识。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 817b9bfc0af054b344ca9f770085ac022a8e6eac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380702"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>使用 Azure PowerShell 分配 Key Vault 访问策略

Key Vault 访问策略确定给定的服务主体（即应用程序或用户组）是否可以对 Key Vault [机密](../secrets/index.yml)、[密钥](../keys/index.yml)和[证书](../certificates/index.yml)执行不同的操作。 可以使用 [Azure 门户](assign-access-policy-portal.md)、[Azure CLI](assign-access-policy-cli.md) 或 Azure PowerShell（本文）来分配访问策略。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

若要详细了解如何使用 Azure PowerShell 在 Azure Active Directory 中创建组，请参阅 [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 和 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)。

## <a name="configure-powershell-and-sign-in"></a>配置 PowerShell 并登录

1. 若要在本地运行命令，请安装 [Azure PowerShell](/powershell/azure/)（如果尚未这样做）。

    若要直接在云中运行命令，请使用 [Azure Cloud Shell](/azure/cloud-shell/overview)。

1. 仅限本地 PowerShell：

    1. 安装 [Azure Active Directory PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD)。

    1. 登录 Azure：

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>获取对象 ID

确定要为其分配访问策略的应用程序、组或用户的对象 ID：

- 应用程序和其他服务主体：使用带有 `-SearchString` 参数的 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) cmdlet 根据所需服务主体的名称筛选结果：

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- 组：使用带有 `-SearchString` 参数的 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) cmdlet 根据所需组的名称筛选结果：

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    在输出中，对象 ID 将作为 `Id` 列出。

- 用户：使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet，将用户的电子邮件地址传递给 `-UserPrincipalName` 参数。

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    在输出中，对象 ID 将作为 `Id` 列出。

## <a name="assign-the-access-policy"></a>分配访问策略

使用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 分配访问策略：

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

为这些特定类型分配权限时，只需提供 `-PermissionsToSecrets`、`-PermissionsToKeys` 和 `-PermissionsToCertificates`。 `<secret-permissions>`、`<key-permissions>` 和 `<certificate-permissions>` 的允许值在 [Set-AzKeyVaultAccessPolicy - 参数](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters)文档中提供。

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性：标识和访问管理](overview-security.md#identity-and-access-management)
- [保护 Key Vault](secure-your-key-vault.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)
- [Azure Key Vault 最佳做法](best-practices.md)
