---
title: "Azure Active Directory 中的 Office 365 组过期 | Microsoft Docs"
description: "如何在 Azure Active Directory 中为 Office 365 组设置过期（预览版）"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 6b454ed7257e8d3f91e585cee2b559c54371fb15
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>配置 Office 365 组过期（预览版）

现在可以通过为 Office 365 组设置过期来管理其生命周期。 可以在 Azure Active Directory (Azure AD) 中为 Office 365 组设置过期。 将某个组设置为过期后：
-   在过期时间即将到来时，通知该组的所有者续订该组
-   未续订的任何组将被删除
-   组所有者或管理员可在 30 日内还原任何被删除的 Office 365 组。

> [!NOTE]
> 为 Office 365 组设置到期时间要求到期时间设置要应用到的组的所有成员具有 Azure AD Premium 许可证。

有关如何下载和安装 Azure AD PowerShell cmdlet 的信息，请参阅 [Azure Active Directory PowerShell for Graph – 公共预览版 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="set-group-expiration"></a>设置组过期

1. 使用 Azure AD 租户中的全局管理员帐户打开 [Azure AD 管理中心](https://aad.portal.azure.com)。

2. 打开 Azure AD，选择“用户和组”。

3. 选择“组设置”，然后选择“过期”以打开过期设置。
  
  ![过期边栏选项卡](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. 在“过期”边栏选项卡中，可以：

  * 设置组的生存期（天）。 可以从预设值中任选其一，或自定义一个值（应为 31 天或以上）。 
  * 指定当组没有所有者时续订和过期通知应发送到的电子邮件地址。 
  * 选择会过期的 Office 365 组。 可以为所有 Office 365 组启用过期，从各 Office 365 组中进行选择，或选择“无”为所有组禁用过期。
  * 设置完成后，选择“保存”来保存设置。


如下电子邮件通知将在组过期前的 30 天、15 天和 1 天发送到 Office 365 组所有者。

![过期电子邮件通知](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

组所有者可以选择“续订组”以续订 Office 365 组，或选择“转到组”查看关于组的成员和其他详细信息。

当某个组过期时，它将在到期日期的后一天被删除。 如下电子邮件通知将发送到 Office 365 组所有者，通知其关于 Office 365 组过期及后续删除的信息。

![组删除电子邮件通知](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

可通过选择“还原组”或使用 PowerShell cmdlet 来还原组，如 [在 Azure Active Directory 中还原已删除的 Office 365 组] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal) 中所述。
    
如果所还原的组包含文档、SharePoint 站点或其他持久对象，可能需要最多 24 小时才能完全还原该组及其内容。

> [!NOTE]
> * 首次设置过期时，早于过期间隔的所有组将设置为 30 天后过期。 首个续订通知电子邮件将在一天内发出。 
>   例如，组 A 创建于 400 天前，而过期间隔设置为 180 天。 当应用过期设置后，组 A 在被删除前还有 30 天（除非所有者续订它）。
> * 当删除并还原动态组时，会将视其为一个新组并根据规则重新填充。 此过程可能最多需要 24 小时。

## <a name="next-steps"></a>后续步骤
以下文章提供有关 Azure AD 组的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员](active-directory-groups-members-azure-portal.md)
* [管理组的成员身份](active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)
