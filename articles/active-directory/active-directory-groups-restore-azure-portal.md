---

title: "在 Azure Active Directory 中还原已删除的 Office 365 组 | Microsoft Docs"
description: "如何在 Azure Active Directory 中还原已删除的组、查看可还原的组，以及永久删除组"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: 795b711542c0a200b80e414397094a102213caf2
ms.contentlocale: zh-cn
ms.lasthandoff: 05/19/2017


---

# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>在 Azure Active Directory 中还原已删除的 Office 365 组

在 Azure Active Directory (Azure AD) 中删除某个 Office 365 组后，删除的组将会保留，但从删除日期开始算起的 30 天内将不可见。 这样，便可以根据需要还原该组及其内容。 此功能仅适用于 Azure AD 中的 Office 365 组， 而不适用于安全组和分发组。

> [!NOTE] 
> 请勿使用 `Remove-MsolGroup`，因为它会永久清除组。 始终使用 `Remove-AzureADMSGroup` 来删除 O365 组。 

还原组所需的权限可能是下列其中一项：

角色  | 权限 
--------- | ---------
公司管理员、合作伙伴 2 级支持人员和 InTune 服务管理员 | 可以还原任何已删除的 Office 365 组 
用户帐户管理员与合作伙伴 1 级支持人员 | 可以还原任何已删除的 Office 365 组，但分配给公司管理员角色的组除外 
用户 | 可以还原他们拥有的任何已删除 Office 365 组 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>查看可还原的已删除 Office 365 组
可以使用以下 cmdlet 查看已删除的组，确认尚未永久清除的、想要还原的一个或多个组。 这些 cmdlet 属于 [Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/)。 [Azure Active Directory PowerShell 版本 2](/powershell/azure/install-adv2?view=azureadps-2.0) 一文中介绍了此模块的详细信息。

1.    运行以下 cmdlet，显示租户中所有已删除的但仍可还原的 Office 365 组。
  ```
  Get-AzureADMSDeletedGroup
  ```

2.    或者，如果你知道特定组的 objectID（可通过步骤 1 中的 cmdlet 来获取该 ID），可运行以下 cmdlet 来确认已删除的但尚未永久清除的特定组。
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>如何还原已删除的 Office 365 组
确认组仍可还原后，可以使用以下步骤之一还原已删除的组。 如果该组包含文档、SP 站点或其他持久对象，可能需要 24 小时才能完全还原该组及其内容。

1.    运行以下 cmdlet 还原该组及其内容。
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

或者，可运行以下 cmdlet 永久清除已删除的组。
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>如何知道是否还原成功？
若要验证是否已成功还原 Office 365 组，请运行 `Get-AzureADGroup –ObjectId <objectId>` cmdlet 显示有关该组的信息。 完成还原请求后：
- 该组将显示在 Exchange 上的左侧导航栏中
- 该组的计划将显示在 Planner 中
- 将显示所有 Sharepoint 站点及其所有内容
- 可从任何 Exchange 终结点以及支持 Office 365 组的其他 Office 365 工作负荷访问该组


## <a name="next-steps"></a>后续步骤
以下文章提供了有关 Azure Active Directory 组的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员](active-directory-groups-members-azure-portal.md)
* [管理组的成员身份](active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)

