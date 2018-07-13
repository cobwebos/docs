---
title: 更新 Azure Stack 用户订阅的所有者 | Microsoft Docs
description: 更改 Azure Stack 用户订阅的计费所有者。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: brenduns
ms.reviewer: shnatara
ms.openlocfilehash: de8610944e11d8cf106ac484a0c6634c8661b5a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009486"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>更改 Azure Stack 用户订阅的所有者

Azure Stack 操作员可以使用 PowerShell 更改用户订阅的计费所有者。 更改所有者的原因之一是替换已离职的用户。   

会将两种类型的所有者分配到订阅：

- **计费所有者**– 默认情况下，计费所有者是从产品/服务中获取的订阅，然后拥有该订阅的计费关系的用户帐户。 此帐户也是订阅的管理员。  在一个订阅中，只能指定一个此类用户帐户。 计费所有者通常是组织或团队主管。 

  使用 PowerShell cmdlet **Set-AzsUserSubscription** 更改计费所有者。  

- **通过 RBAC 角色添加的所有者**– 可以授予所有者角色使用的其他用户[Role Based Access Control](azure-stack-manage-permissions.md) (RBAC) 系统。  可将任意数量的其他用户帐户添加为所有者，以补充计费所有者。 其他所有者也是订阅的管理员，拥有订阅的所有特权，但无权删除计费所有者。 

  可以使用 PowerShell 管理其他所有者，具体请参阅[使用 Azure PowerShell 管理基于角色的访问控制]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)。


## <a name="change-the-billing-owner"></a>更改计费所有者
运行以下脚本更改用户订阅的计费所有者。  用于运行该脚本的计算机必须连接到 Azure Stack 并运行 Azure Stack PowerShell 模块 1.3.0 或更高版本。 有关详细信息，请参阅[安装 Azure Stack PowerShell](azure-stack-powershell-install.md)。 

> [!Note]  
>  在多租户 Azure Stack 中，新的所有者必须与现有的所有者相同的目录中。 你可以向另一个目录中的用户提供订阅的所有权之前，你必须首先[到你的目录作为来宾邀请该用户](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。 


运行脚本之前，请替换脚本中的以下值： 
 
- **$ArmEndpoint** – 指定您的环境的资源管理器终结点。  
- **$TenantId** - 指定租户 ID。 
- **$SubscriptionId** – 指定订阅 id。
- **$OwnerUpn** - 以 *user@example.com* 格式指定要添加为新计费所有者的帐户。  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>后续步骤
[管理基于角色的访问控制](azure-stack-manage-permissions.md)
