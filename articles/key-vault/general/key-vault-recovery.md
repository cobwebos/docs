---
title: Azure Key Vault 恢复概述 |Microsoft Docs
description: Key Vault 恢复功能旨在防止意外或恶意删除密钥保管库以及密钥保管库中存储的机密、密钥和证书。
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
manager: ravijan
ms.date: 09/30/2020
ms.openlocfilehash: bea4a5d92309710645dd63e611cd0a5e3b742c34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604228"
---
# <a name="azure-key-vault-recovery-overview"></a>Azure Key Vault 恢复概述

本文介绍了 Azure Key Vault、软删除和清除保护的两种恢复功能。 本文档概述了这些功能，并演示了如何通过 Azure 门户、Azure CLI 和 Azure PowerShell 对其进行管理。

## <a name="what-are-soft-delete-and-purge-protection"></a>什么是软删除和清除保护

软删除和清除保护是两种不同的密钥保管库恢复功能。
> [!IMPORTANT]
> 需要在所有密钥保管库上启用软删除保护。 2020年12月将弃用禁用软删除保护的功能。 请 [**在此处**查看完整的详细信息。](soft-delete-change.md)

**软删除** 旨在防止意外删除密钥保管库、密钥、机密以及密钥保管库中存储的证书。 像回收站一样，将软删除视为软删除。 删除 key vault 或 key vault 对象时，它将保持可恢复的用户可配置的保留期，或默认为90天。 还可以 **清除** 软删除状态的密钥保管库，这意味着它们将被永久删除。 这样，便可以重新创建具有相同名称的密钥保管库和密钥保管库对象。 恢复和删除密钥保管库和对象都需要提升的访问策略权限。 **启用软删除后，将无法禁用。**

请务必注意， **密钥保管库名称是全局唯一**的，因此无法在软删除状态下创建与密钥保管库同名的密钥保管库。 同样，密钥、机密和证书的名称在密钥保管库中是唯一的。 你将无法使用 "软删除" 状态中的其他名称创建机密、密钥或证书。

**清除保护** 旨在防止恶意内部成员删除密钥保管库、密钥、机密和证书。 使用基于时间的锁将此视为回收站。 可在可配置的保留期内随时恢复项。 **在保留期结束之前，你将无法永久删除或清除密钥保管库。** 保持期结束后，将自动清除密钥保管库或 key vault 对象。

> [!NOTE]
> 清除保护旨在使管理员角色或权限不能覆盖、禁用或绕过清除保护。 **一旦启用清除保护，任何人（包括 Microsoft）都不能禁用或重写。** 这意味着，在重复使用密钥保管库名称之前，必须恢复已删除的密钥保管库或等待保持期。

# <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>验证是否对密钥保管库启用了软删除并启用软删除

1. 登录到 Azure 门户。
1. 选择密钥保管库。
1. 单击 "属性" 边栏选项卡。
1. 验证软删除旁边的单选按钮是否设置为 "启用恢复"。
1. 如果未对 key vault 启用软删除，请单击单选按钮以启用软删除，并单击 "保存"。

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="<Azure 门户的屏幕快照>":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>向服务主体授予访问权限以清除和恢复已删除的机密

1. 登录到 Azure 门户。
1. 选择密钥保管库。
1. 单击 "访问策略" 边栏选项卡。
1. 在表中，找到要向其授予访问权限的安全主体的行 (或添加新的安全主体) 。
1. 单击 "密钥"、"证书" 和 "机密"。
1. 滚动到下拉菜单底部，然后单击 "恢复" 和 "清除"
1. 安全主体还需要 get 和 list 功能才能执行大多数操作。

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="<Azure 门户的屏幕快照>":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>列出、恢复或清除软删除的密钥保管库

1. 登录到 Azure 门户。
1. 单击页面顶部的搜索栏。
1. 在 "最近使用的服务" 下，单击 "Key Vault"。 不要单击单个密钥保管库。
1. 在屏幕顶部，单击 "管理已删除的保管库" 选项
1. 上下文窗格会在屏幕的右侧打开。
1. 选择订阅。
1. 如果已软删除 key vault，它将显示在右侧的上下文窗格中。
1. 如果保管库过多，可以单击上下文窗格底部的 "加载更多"，也可以使用 CLI 或 PowerShell 获取结果。
1. 找到要恢复或清除的保管库后，选择它旁边的复选框。
1. 如果要恢复密钥保管库，请选择 "上下文" 窗格底部的 "恢复" 选项。
1. 如果要永久删除密钥保管库，请选择 "清除" 选项。

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="<Azure 门户的屏幕快照>":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="<Azure 门户的屏幕快照>":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>列出、恢复或清除软删除的机密、密钥和证书

1. 登录到 Azure 门户。
1. 选择密钥保管库。
1. 选择与你想要管理的机密类型对应的边栏选项卡， (密钥、机密或证书) 。
1. 在屏幕顶部，单击 "管理已删除 (密钥、机密或证书) 
1. 上下文窗格将出现在屏幕的右侧。
1. 如果密钥、密钥或证书未显示在列表中，则它不会处于软删除状态。
1. 选择要管理的机密、密钥或证书。
1. 选择 "恢复" 或 "清除" 选项。

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="<Azure 门户的屏幕快照>":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI) 

* 验证是否已启用软删除的密钥保管库

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 在密钥保管库上启用软删除

    默认情况下，所有新的密钥保管库均已启用软删除。 如果当前有未启用软删除的密钥保管库，请使用以下命令启用软删除。

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* 如果启用软删除，则删除密钥保管库 (可恢复) 

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 列出所有软删除的密钥保管库

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* 恢复软删除的密钥保管库

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 清除软删除的密钥保管库 ** (警告！此操作将永久删除密钥保管库) **

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 对密钥保管库启用清除保护

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a> (CLI 的证书) 

* 授予对清除和恢复证书的访问权限

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* 删除证书

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 列出已删除的证书

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 恢复删除的证书

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 清除软删除的证书 ** (警告！此操作将永久删除你的证书) **

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>CLI)  (密钥

* 授予对清除和恢复密钥的访问权限

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* 删除密钥

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 列出已删除的密钥

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 恢复删除的密钥

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 清除软删除的密钥 ** (警告！此操作将永久删除你的密钥) **

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a> (CLI) 的机密

* 授予清除和恢复机密的访问权限

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* 删除机密

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 列出已删除的机密

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 恢复删除的机密

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 清除软删除的机密 ** (警告！此操作将永久删除你的机密) **

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a> (PowerShell) Key Vault

* 验证是否已启用软删除的密钥保管库

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* 在密钥保管库上启用软删除

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

* 删除密钥保管库

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* 列出所有软删除的密钥保管库

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* 恢复软删除的密钥保管库

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* 清除软删除的密钥保管库 ** (警告！此操作将永久删除密钥保管库) **

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* 对密钥保管库启用清除保护

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a> (PowerShell) 的证书

* 授予恢复和清除证书的权限

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* 删除证书

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* 列出密钥保管库中所有已删除的证书

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* 恢复处于已删除状态的证书

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* 清除软删除的证书 ** (WARNING！此操作将永久删除你的证书) **

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a> (PowerShell) 的密钥

* 授予恢复和清除密钥的权限

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* 删除密钥

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* 列出密钥保管库中所有已删除的证书

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* 恢复软删除密钥

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* 清除软删除的密钥 ** (警告！此操作将永久删除你的密钥) **

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a> (PowerShell) 的机密

* 授予恢复和清除机密的权限

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* 删除名为 SQLPassword 的机密

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* 列出密钥保管库中所有已删除的机密

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* 恢复处于已删除状态的机密

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* 清除处于已删除状态的机密 ** (警告！此操作将永久删除你的密钥) **

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
