---
title: 更新 Azure Stack 用户订阅的所有者 | Microsoft Docs
description: 更改 Azure Stack 用户订阅的账单所有者。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: 46dd9c786fddb2f6ce9fb8a761a1b9f135a82d74
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959190"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>更改 Azure Stack 用户订阅的所有者

Azure Stack 操作员可以使用 PowerShell 更改用户订阅的账单所有者。 例如，更改的所有者的一个原因是替换用户离开你的组织。   

会将两种类型的所有者分配到订阅：

- **计费所有者**： 默认情况下的账单所有者是从产品/服务中获取的订阅，然后拥有该订阅的计费关系的用户帐户。 此帐户也是订阅的管理员。 在一个订阅中，只能指定一个此类用户帐户。 账单所有者通常是组织或团队主管。 

  使用 PowerShell cmdlet[集 AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription)若要更改的账单所有者。  

- **通过 RBAC 角色添加的所有者**– 可以向其他用户授予**所有者**角色使用[Role Based Access Control](azure-stack-manage-permissions.md) (RBAC) 系统。 可以是任意数量的其他用户帐户添加为所有者来补充的账单所有者。 更多所有者也是订阅的管理员和具有所有权限的订阅，除非删除的账单所有者的权限。 

  可以使用 PowerShell 管理其他所有者，具体请参阅[使用 Azure PowerShell 管理基于角色的访问控制](/azure/role-based-access-control/role-assignments-powershell)。

## <a name="change-the-billing-owner"></a>更改的账单所有者

运行以下脚本来更改用户订阅的账单所有者。 用于运行该脚本的计算机必须连接到 Azure Stack 并运行 Azure Stack PowerShell 模块 1.3.0 或更高版本。 有关详细信息，请参阅[安装 Azure Stack PowerShell](azure-stack-powershell-install.md)。 

> [!Note]
>  在多租户 Azure Stack，新的所有者必须与现有的所有者相同的目录中。 你可以向另一个目录中的用户提供订阅的所有权之前，你必须首先[到你的目录作为来宾邀请该用户](../active-directory/b2b/add-users-administrator.md)。 

运行脚本之前，请替换脚本中的以下值： 
 
- **$ArmEndpoint**： 指定你的环境的资源管理器终结点。  
- **$TenantId**： 指定你的租户 id。 
- **$SubscriptionId**： 指定你的订阅 id。
- **$OwnerUpn**： 指定一个帐户作为**user@example.com**将添加为新的账单所有者。  

```PowerShell   
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>后续步骤

[管理基于角色的访问控制](azure-stack-manage-permissions.md)
