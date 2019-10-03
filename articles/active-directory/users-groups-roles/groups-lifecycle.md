---
title: 为 Office 365 组设置过期 - Azure Active Directory | Microsoft Docs
description: 如何在 Azure Active Directory 中为 Office 365 组设置过期
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56bfe92de24b9386252ee8719af66cc658948565
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844314"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>为 Office 365 组配置过期策略

本文说明如何通过为 Office 365 组设置过期策略来管理这些组的生命周期。 仅可以在 Azure Active Directory (Azure AD) 中为 Office 365 组设置过期策略。

将某个组设置为过期后：

- 在过期时间即将到来时，通知该组的所有者续订该组
- 未续订的任何组将被删除
- 组所有者或管理员可在 30 日内还原任何被删除的 Office 365 组。

当前只能为租户上的 Office 365 组配置一个过期策略。

> [!NOTE]
> 为 Office 365 组配置和使用过期策略要求你拥有（但不一定）为应用过期策略的所有组的成员分配 Azure AD Premium 许可证。

有关如何下载和安装 Azure AD PowerShell cmdlet 的信息，请参阅 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="roles-and-permissions"></a>角色和权限

以下角色可用于在 Azure AD 中为 Office 365 组配置和使用到期。

Role | 权限
-------- | --------
全局管理员或用户管理员 | 可以创建、读取、更新或删除 Office 365 组到期策略设置<br>可以续订任何 Office 365 组
用户 | 可以续订他们拥有的 Office 365 组<br>可以还原他们拥有的 Office 365 组<br>可以读取过期策略设置

有关还原已删除组的权限的详细信息，请参阅[在 Azure Active Directory 中还原已删除的 Office 365 组](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>设置组过期

1. 使用 Azure AD 组织中的全局管理员帐户打开[Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择 "**组**"，然后选择 "**过期**" 以打开过期设置。
  
   ![组的过期设置](./media/groups-lifecycle/expiration-settings.png)

3. 在 "**过期**" 页上，您可以：

  - 设置组的生存期（天）。 可以从预设值中任选其一，或自定义一个值（应为 31 天或以上）。
  - 指定当组没有所有者时续订和过期通知应发送到的电子邮件地址。
  - 选择会过期的 Office 365 组。 可以为以下内容设置过期时间：
    - **全部**Office 365 组
    - **所选**Office 365 组的列表
    - **无**以限制所有组的过期时间
  - 设置完成后，选择“保存”来保存设置。

> [!NOTE]
> 首次设置过期时，早于过期时间间隔的任何组都将设置为30天直到过期，除非所有者续订。 首个续订通知电子邮件将在一天内发出。
>
> 删除并还原动态组时，会将其视为新组，并根据规则重新填充。 此过程可能需要长达24小时。
>
> 团队中使用的组的过期通知出现在 "团队所有者" 源中。

## <a name="email-notifications"></a>电子邮件通知

如下电子邮件通知将在组过期前的 30 天、15 天和 1 天发送到 Office 365 组所有者。 电子邮件的语言由组所有者首选语言或 Azure AD 语言设置确定。 如果组所有者定义了首选语言，或多个所有者具有相同首选语言，则使用该语言。 对于所有其他情况，将使用 Azure AD 语言设置。

![过期电子邮件通知](./media/groups-lifecycle/expiration-notification.png)

组所有者可以从“续订组”通知电子邮件直接访问“访问面板”中的组详细信息页。 在此处，用户可以获取更多关于组的信息，例如该组的描述、上次续订时间、到期时间，并且还能续订组。 组详细信息页面现在还包括 Office 365 组资源的链接，因此组所有者可以在组中轻松查看内容和活动。

当某个组过期时，它将在到期日期的后一天被删除。 如下电子邮件通知将发送到 Office 365 组所有者，通知其关于 Office 365 组过期及后续删除的信息。

![组删除电子邮件通知](./media/groups-lifecycle/deletion-notification.png)

可以通过选择“还原组”或使用 PowerShell cmdlet 在删除组后的 30 天内还原组，如[在 Azure Active Directory 中还原已删除的 Office 365 组](groups-restore-deleted.md)中所述。 请注意，30 天的组还原期不可自定义。

如果所还原的组包含文档、SharePoint 站点或其他持久对象，可能需要最多 24 小时才能完全还原该组及其内容。

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>如何检索 Office 365 组到期日期
除了访问面板，用户可以在其中查看组详细信息（包括到期日期和上次续订日期），可以从 Microsoft Graph REST API Beta 检索 Office 365 组的到期日期。 Microsoft Graph Beta 中已启用 expirationDateTime 作为组属性。 可以使用 GET 请求来检索它。 有关更多详细信息，请参阅[此示例](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)。

> [!NOTE]
> 若要在访问面板上管理组成员身份，需要在 Azure Active Directory 组 "常规" 设置中将 "限制访问访问面板中的组" 设置为 "否"。

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 组到期与合法保留邮箱一起使用的方式
组到期并删除后，应用（例如 Planner、站点或 Teams）中删除的组数据将会在 30 天后永久删除，但合法保留的组邮箱将会保留下来并且不会永久删除。 管理员可以使用 Exchange cmdlets 来恢复邮箱以提取数据。 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 组到期与保留策略一起使用的方式
保留策略是通过安全和合规中心配置的。 如果为 Office 365 组设置了保留策略，组到期并被删除后，组邮箱中的组对话和组站点中的文件将保留在保留容器中，保留的特定天数取决于保留策略中的定义。 到期后，用户无法查看组或其中的内容，但可以通过电子发现恢复站点和邮箱数据。

## <a name="powershell-examples"></a>PowerShell 示例
以下示例演示了如何使用 PowerShell cmdlet 在 Azure AD 组织中配置 Office 365 组的过期设置：

1. 安装 PowerShell v2.0 模块并在 PowerShell 提示符下登录：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 配置过期设置使用 Remove-azureadmsgrouplifecyclepolicy cmdlet 将 Azure AD 组织中所有 Office 365 组的生存期设置为365天。 没有所有者的 Office 365 组的续订通知将发送到“emailaddress@contoso.com”
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 检索现有策略 Get-AzureADMSGroupLifecyclePolicy：此 cmdlet 检索已配置的当前 Office 365 组到期设置。 在此示例中，可以看到：

   - 策略 ID
   - Azure AD 组织中所有 Office 365 组的生存期设置为365天
   - 没有所有者的 Office 365 组的续订通知将发送到“emailaddress@contoso.com”。
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. 更新现有策略 Set-AzureADMSGroupLifecyclePolicy：此 cmdlet 用于更新现有策略。 在以下示例中，现有策略中的组生存期从 365 天更改为 180 天。
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. 将特定组添加到策略 Add-AzureADMSLifecyclePolicyGroup：此 cmdlet 将组添加到生命周期策略。 示例：
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. 删除现有策略 Remove-AzureADMSGroupLifecyclePolicy：此 cmdlet 删除 Office 365 组到期设置，但需要策略 ID。 这将禁用 Office 365 组过期。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
以下 cmdlet 可用于更详细地配置策略。 有关详细信息，请参阅[PowerShell 文档](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)。

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>后续步骤

以下文章提供有关 Azure AD 组的更多信息。

- [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理组的成员](../fundamentals/active-directory-groups-members-azure-portal.md)
- [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-dynamic-membership.md)
