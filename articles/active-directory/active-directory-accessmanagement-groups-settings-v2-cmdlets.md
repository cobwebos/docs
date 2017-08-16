---
title: "用于在 Azure AD 中进行组管理的 Azure Active Directory PowerShell cmdlet | Microsoft 文档"
description: "本页提供的 PowerShell 示例适用于在 Azure Active Directory 中管理组"
keywords: "Azure AD, Azure Active Directory, PowerShell, 组, 组管理"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.reviewer: rodejo
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: c2a313c5ad011d03309a962bf2905750a478b890
ms.contentlocale: zh-cn
ms.lasthandoff: 08/05/2017

---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>用于组管理的 Azure Active Directory 版本 2 cmdlet
> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-groups-create-azure-portal.md)
> * [Azure 经典门户](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

以下文档提供的示例介绍如何使用 PowerShell 在 Azure Active Directory (Azure AD) 中管理组。  该文档还介绍如何使用 Azure AD PowerShell 模块进行设置。 首先，必须[下载 Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/)。

## <a name="installing-the-azure-ad-powershell-module"></a>安装 Azure AD PowerShell 模块
若要安装 AzureAD PowerShell 模块，请使用以下命令：

    PS C:\Windows\system32> install-module azuread

若要验证模块是否已安装，请使用以下命令：

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

现在可以开始使用模块中的 cmdlet 了。 有关 Azure AD 模块中 cmdlet 的完整说明，请参阅[联机参考文档](/powershell/azure/install-adv2?view=azureadps-2.0)。

## <a name="connecting-to-the-directory"></a>连接到目录
在开始使用 Azure AD PowerShell cmdlet 管理组之前，必须将 PowerShell 会话连接到要管理的目录。 为此，请使用以下命令：

    PS C:\Windows\system32> Connect-AzureAD

该 cmdlet 会提示用户输入访问目录时需要使用的凭据。 在此示例中，我们将使用 karen@drumkit.onmicrosoft.com 访问演示目录。 该 cmdlet 会返回一个确认，表明会话已成功连接到目录：

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

现在可以开始使用 AzureAD cmdlet 管理目录中的组。

## <a name="retrieving-groups"></a>检索组
若要从目录中检索现有组，可使用 Get-AzureADGroups cmdlet。 若要检索目录中的所有组，请使用不带参数的 cmdlet：

    PS C:\Windows\system32> get-azureadgroup

该 cmdlet 会返回已连接目录中的所有组。

可以使用 -objectID 参数检索已指定组 objectID 的特定组：

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

该 cmdlet 现在会返回其 objectID 与用户输入的参数值匹配的组：

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

可以使用 -filter 参数搜索特定组。 此参数采用 ODATA 筛选器子句，并返回与筛选器匹配的所有组，如以下示例所示：

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

请注意，AzureAD PowerShell cmdlet 实施 OData 查询标准。如需详细信息，可单击[此处](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)。

## <a name="creating-groups"></a>创建组
若要在目录中创建新的组，可使用 New-AzureADGroup cmdlet。 此 cmdlet 创建名为“Marketing”的新安全组：

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>更新组
若要更新现有组，请使用 Set-AzureADGroup cmdlet。 在此示例中，我们将更改“Intune 管理员”组的 DisplayName 属性。 首先，我们发现使用 Get-AzureADGroup cmdlet 的组和使用 DisplayName 属性的筛选器：

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

接下来，我们会将“说明”属性更改为新值“Intune 设备管理员”：

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

现在如果再次查找该组，我们就会发现，“说明”属性已用新值更新：

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>删除组
若要从目录中删除组，请使用 Remove-AzureADGroup cmdlet，如下所示：

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>管理组成员
如需向组添加新成员，请使用 Add-AzureADGroupMember cmdlet。 该命令将成员添加到我们在上一示例中使用的“Intune 管理员”组：

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

#NAME?

若要获取组的现有成员，请使用 Get-AzureADGroupMember cmdlet，如以下示例所示：

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

若要删除我们以前添加到组的成员，请使用 Remove-AzureADGroupMember cmdlet，如下所示：

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

若要验证用户的组成员身份，请使用 Select-AzureADGroupIdsUserIsMemberOf cmdlet。 该 cmdlet 使用用户的 ObjectId 作为参数，以便检查组成员身份；同时使用组列表作为参数来检查成员身份。 组列表必须以类型为“Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck”的复合变量形式提供，因此必须先创建该类型的变量：

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

接下来提供可在该复合变量的“GroupIds”属性中查看的 groupIds 的值：

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

现在，如果我们需要针对 $g 中的组查看 ObjectID 为 72cd4bbd-2594-40a2-935c-016f3cfeeeea 的用户的组成员身份，则应使用：

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


返回的值是该用户所在组的列表。 也可通过 Select-AzureADGroupIdsContactIsMemberOf、Select-AzureADGroupIdsGroupIsMemberOf 或 Select-AzureADGroupIdsServicePrincipalIsMemberOf 应用此方法，检查特定组列表的联系人、组或服务主体成员身份

## <a name="managing-owners-of-groups"></a>管理组的所有者
若要向组添加所有者，请使用 Add-AzureADGroupOwner cmdlet：

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

#NAME?

若要检索组的所有者，请使用 Get-AzureADGroupOwner：

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

该 cmdlet 将返回指定组的所有者的列表：

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

若需从组中删除所有者，请使用 Remove-AzureADGroupOwner：

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>保留的别名 
创建组后，某些终结点允许最终用户指定一个 mailNickname 或别名，用作组的电子邮件地址的一部分。   
仅 Azure AD 全局管理员可以创建具有以下权限较高的电子邮件别名的组。 
  
* abuse 
* admin 
* administrator 
* hostmaster 
* majordomo 
* postmaster 
* root 
* secure 
* security 
* ssl-admin 
* webmaster 

## <a name="next-steps"></a>后续步骤
如需更多 Azure Active Directory PowerShell 文档，可参阅 [Azure Active Directory Cmdlet](/powershell/azure/install-adv2?view=azureadps-2.0)。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

