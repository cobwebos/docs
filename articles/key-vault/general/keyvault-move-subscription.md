---
title: Azure Key Vault 将保管库移动到不同的订阅 |Microsoft Docs
description: 有关将密钥保管库移动到不同订阅的指南。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 4046d4ec5f62ffc4fab50e8c5a4a08fad326aa04
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796949"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>将 Azure Key Vault 移到另一个订阅

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

**将密钥保管库移到另一个订阅将导致环境的重大更改。**

请确保了解此更改的影响，并仔细按照本文中的指南进行操作，然后再决定将密钥保管库移动到新的订阅。

创建密钥保管库时，该密钥保管库自动绑定到创建该密钥保管库的订阅的默认 Azure Active Directory 租户 ID。 所有访问策略条目也都绑定到此租户 ID。 如果将 Azure 订阅从租户 A 移到租户 B，则租户 B 中的服务主体（用户和应用程序）将无法访问现有的密钥保管库。若要解决此问题，需要执行以下操作：

* 将与订阅中所有现有密钥保管库关联的租户 ID 更改到租户 B。
* 删除所有现有的访问策略条目。
* 添加与租户 B 关联的新访问策略条目。

## <a name="limitations"></a>限制

某些服务主体（用户和应用程序）绑定到特定的租户。 如果将 key vault 移到另一个租户中的订阅，可能无法还原对特定服务主体的访问权限。 检查以确保在要移动密钥保管库的租户中存在所有基本的服务主体。

## <a name="design-considerations"></a>设计注意事项

你的组织可能已在订阅级别实施了强制或排除的 Azure 策略。 在订阅中可能会有一组不同的策略分配，其中当前存在密钥保管库，以及要在其中移动密钥保管库的订阅。 策略要求冲突可能会破坏应用程序。

### <a name="example"></a>示例

已将应用程序连接到密钥保管库，该密钥保管库创建的证书有效期为两年。 尝试移动密钥保管库的订阅有一个策略分配，阻止创建有效期超过一年的证书。 将密钥保管库移动到新订阅后，创建有效证书的操作将被 Azure 策略分配阻止。

### <a name="solution"></a>解决方案

请确保转到 Azure 门户上的 "Azure 策略" 页，并查看当前订阅的策略分配以及要移到的订阅，并确保没有不匹配。

## <a name="prerequisites"></a>先决条件

* 参与者级别的访问权限或更高级别，用于在密钥保管库所在的当前订阅。
* 参与者级别访问权限或更高级别，用于要移动密钥保管库的订阅。
* 新订阅中的资源组。

## <a name="procedure"></a>过程

### <a name="initial-steps-moving-key-vault"></a>初始步骤（移动 Key Vault）

1. 登录到 Azure 门户
2. 导航到密钥保管库
3. 单击 "概述" 选项卡
4. 选择 "移动" 按钮
5. 从下拉选项中选择 "移动到另一个订阅"
6. 选择要在其中移动密钥保管库的资源组
7. 选择要在其中移动密钥保管库的资源组
8. 确认有关移动资源的警告
9. 选择“确定”

### <a name="additional-steps-post-move"></a>其他步骤（移动后）

将密钥保管库移动到新订阅后，需要更新租户 ID 并删除旧的访问策略。 以下教程介绍了如何在 PowerShell 中执行这些步骤，并 Azure CLI。

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

既然保管库已与正确的租户 ID 关联，并且旧的访问策略条目已删除，请使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet 或 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令设置新的访问策略条目。

如果使用 Azure 资源的托管标识，则还需要将其更新为新的 Azure AD 租户。 有关托管标识的详细信息，请参阅[使用托管标识提供 Key Vault 身份验证](managed-identity.md)。

如果使用的是 MSI，则还必须更新 MSI 标识，因为旧标识将不再位于相应的 AAD 租户中。


