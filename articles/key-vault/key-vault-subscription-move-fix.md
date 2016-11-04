---
title: 订阅移动后更改密钥保管库租户 ID | Microsoft Docs
description: 了解如何在订阅移至不同的租户后切换密钥保管库的租户 ID
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# 订阅移动后更改密钥保管库租户 ID
### 问：我的订阅已从租户 A 移动到租户 B。如何更改我的现有密钥保管库的租户 ID，并为租户 B 中的主体设置正确的 ACL？
在订阅中创建新的密钥保管库时，它将自动关联到该订阅的默认 Azure Active Directory 租户 ID。所有访问策略条目也都绑定到此租户 ID。当 Azure 订阅从租户 A 移到租户 B 时，租户 B 中的主体将无法访问现有的密钥保管库（用户和应用程序）。若要解决此问题，需要

* 将此订阅中与所有现有密钥保管库关联的租户 ID 更改到租户 B
* 删除所有现有的访问策略条目
* 添加与租户 B 相关联的新访问策略条目。

例如，如果在从租户 A 移到租户 B 的订阅中有密钥保管库“myvault”，以下演示了如何更改此密钥保管库的租户 ID，并删除旧的访问策略。

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

在移动原始值之前，此保管库在租户 A 中，**$vault.Properties.TenantId** 为租户 A，同时 **(Get-AzureRmContext).Tenant.TenantId** 为租户 B。

现在，你的保管库与正确的租户 ID 相关联，并且会删除旧的访问策略条目，请使用 [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx) 设置新的访问策略条目。

## 后续步骤
* 如果对密钥保管库有任何疑问，请访问 [Azure 密钥保管库论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->