---
title: 订阅移动后更改密钥保管库租户 ID - Azure 密钥保管库 | Microsoft Docs
description: 了解如何在订阅移至不同的租户后切换密钥保管库的租户 ID
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: f32146697be234a8a288ff991b1f7adf6e76dc7e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724491"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>订阅移动后更改密钥保管库租户 ID

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>问：我的订阅已从租户 A 移动到租户 B。如何更改我的现有密钥保管库的租户 ID，并为租户 B 中的主体设置正确的 ACL？

在订阅中创建新的密钥保管库时，该密钥保管库自动绑定到该订阅的默认 Azure Active Directory 租户 ID。 所有访问策略条目也都绑定到此租户 ID。 将 Azure 订阅从租户 A 移到租户 B 时，租户 B 中的主体（用户和应用程序）无法访问现有的密钥保管库。若要解决此问题，需要执行以下操作：

* 将此订阅中与所有现有密钥保管库关联的租户 ID 更改为租户 B 的 ID。
* 删除所有现有的访问策略条目。
* 添加与租户 B 相关联的新访问策略条目。

例如，如果在从租户 A 移到租户 B 的订阅中有密钥保管库“myvault”，以下演示了如何更改此密钥保管库的租户 ID，并删除旧的访问策略。

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

因为移动前此保管库位于租户 A，所以“$vault.Properties.TenantId”的原始值为租户 A，而“(Get-AzContext).Tenant.TenantId”的值为租户 B。

现在，保管库与正确的租户 ID 相关联，并且会删除旧的访问策略条目，请使用 [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) 设置新的访问策略条目。

## <a name="next-steps"></a>后续步骤

如果对 Azure 密钥保管库有任何疑问，请访问 [Azure 密钥保管库论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
