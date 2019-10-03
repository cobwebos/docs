---
title: 订阅移动后更改密钥保管库租户 ID - Azure 密钥保管库 | Microsoft Docs
description: 了解如何在订阅移至不同的租户后切换密钥保管库的租户 ID
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 2159b5b515e22458edf3ba0eb5b6f23f3f37ce95
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990104"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>订阅移动后更改密钥保管库租户 ID

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>问：我的订阅已从租户 A 移动到租户 B。如何更改我的现有密钥保管库的租户 ID，并为租户 B 中的主体设置正确的 ACL？

在订阅中创建新的密钥保管库时，该密钥保管库自动绑定到该订阅的默认 Azure Active Directory 租户 ID。 所有访问策略条目也都绑定到此租户 ID。 将 Azure 订阅从租户 A 移到租户 B 时，租户 B 中的主体（用户和应用程序）无法访问现有的密钥保管库。要解决此问题，需要执行以下操作：

* 将此订阅中与所有现有密钥保管库关联的租户 ID 更改为租户 B 的 ID。
* 删除所有现有的访问策略条目。
* 添加与租户 B 相关联的新访问策略条目。

例如，如果在从租户 A 移到租户 B 的订阅中有密钥保管库“myvault”，以下演示了如何更改此密钥保管库的租户 ID，并删除旧的访问策略。

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

因为移动前此保管库位于租户 A，所以“$vault.Properties.TenantId”的原始值为租户 A，而“(Get-AzContext).Tenant.TenantId”的值为租户 B   。

现在，保管库与正确的租户 ID 相关联，并且会删除旧的访问策略条目，请使用 [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) 设置新的访问策略条目。

## <a name="next-steps"></a>后续步骤

如果对 Azure 密钥保管库有任何疑问，请访问 [Azure 密钥保管库论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
