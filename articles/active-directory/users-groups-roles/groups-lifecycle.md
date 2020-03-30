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
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048253"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>为 Office 365 组配置过期策略

本文介绍如何通过为 Office 365 组设置过期策略来管理它们的生命周期。 只能为 Azure 活动目录 （Azure AD） 中的 Office 365 组设置过期策略。

将某个组设置为过期后：

- 随着过期的临近，具有用户活动的组将自动续订。
- 如果组未自动续订，则会通知组的所有者续订该组。
- 任何未续订的组都将被删除。
- 任何已删除的 Office 365 组都可以在 30 天内由组所有者或管理员还原。

目前，只能为 Azure AD 组织中的所有 Office 365 组配置一个过期策略。

> [!NOTE]
> 配置和使用 Office 365 组的过期策略要求您拥有但不一定为应用过期策略的所有组的成员分配 Azure AD 高级许可证。

有关如何下载和安装 Azure AD PowerShell cmdlet 的信息，请参阅 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="activity-based-automatic-renewal"></a>基于活动的自动续订

使用 Azure AD 智能，现在根据是否最近使用组自动续订。 此功能消除了组所有者手动操作的需要，因为它基于跨 Office 365 服务（如 Outlook、SharePoint 或 Teams）的组中的用户活动。 例如，如果所有者或组成员执行诸如在 SharePoint 中上载文档、访问 Teams 通道或向 Outlook 中的组发送电子邮件等，则该组将自动续订，并且所有者不会收到任何续订通知。

### <a name="activities-that-automatically-renew-group-expiration"></a>自动续订组过期的活动

以下用户操作会导致自动组续订：

- SharePoint：查看、编辑、下载、移动、共享或上传文件
- Outlook：加入组，从组空间读取/写入组消息，类似于消息（在 Outlook Web 访问中）
- 团队：访问团队通道

### <a name="auditing-and-reporting"></a>审核和报告

管理员可以从 Azure AD 中的活动审核日志中获取自动续订的组的列表。

![基于活动自动续订组](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>角色和权限

以下角色可用于在 Azure AD 中为 Office 365 组配置和使用到期。

角色 | 权限
-------- | --------
全局管理员、组管理员或用户管理员 | 可以创建、读取、更新或删除 Office 365 组到期策略设置<br>可以续订任何 Office 365 组
用户 | 可以续订他们拥有的 Office 365 组<br>可以还原他们拥有的 Office 365 组<br>可以读取过期策略设置

有关还原已删除组的权限的详细信息，请参阅[在 Azure Active Directory 中还原已删除的 Office 365 组](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>设置组过期

1. 使用 Azure AD 组织中全局管理员的帐户打开[Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择 **"组**"，然后选择 **"过期"** 以打开过期设置。
  
   ![组的过期设置](./media/groups-lifecycle/expiration-settings.png)

3. 在 **"过期"** 页上，您可以：

    - 设置组的生存期（天）。 可以从预设值中任选其一，或自定义一个值（应为 31 天或以上）。
    - 指定当组没有所有者时续订和过期通知应发送到的电子邮件地址。
    - 选择会过期的 Office 365 组。 您可以将过期设置为：
      - **全部**办公室 365 个组
      - **所选**办公室 365 个组的列表
      - **无**限制所有组的过期
    - 设置完成后，选择“保存”来保存设置****。

> [!NOTE]
> - 首次设置过期时，任何早于过期间隔的组将设置为 35 天，直到过期，除非该组自动续订或所有者重新续订它。
> - 删除并还原动态组时，它被视为新组，并根据规则重新填充。 此过程最多可能需要 24 小时。
> - Teams 中使用的组的过期通知将显示在"团队所有者"源中。

## <a name="email-notifications"></a>电子邮件通知

如果未自动续订组，则此类电子邮件通知将在组过期前 30 天、15 天和 1 天发送给 Office 365 组所有者。 电子邮件的语言由组所有者的首选语言或 Azure AD 语言设置决定。 如果组所有者定义了首选语言，或多个所有者具有相同首选语言，则使用该语言。 对于所有其他情况，使用 Azure AD 语言设置。

![过期电子邮件通知](./media/groups-lifecycle/expiration-notification.png)

从 **"续订组**通知电子邮件"中，组所有者可以直接访问[访问面板](https://account.activedirectory.windowsazure.com/r#/applications)中的组详细信息页面。 在此处，用户可以获取更多关于组的信息，例如该组的描述、上次续订时间、到期时间，并且还能续订组。 组详细信息页面现在还包括 Office 365 组资源的链接，因此组所有者可以在组中轻松查看内容和活动。

当某个组过期时，它将在到期日期的后一天被删除。 如下电子邮件通知将发送到 Office 365 组所有者，通知其关于 Office 365 组过期及后续删除的信息。

![组删除电子邮件通知](./media/groups-lifecycle/deletion-notification.png)

可以通过选择“还原组”或使用 PowerShell cmdlet 在删除组后的 30 天内还原组，如[在 Azure Active Directory 中还原已删除的 Office 365 组](groups-restore-deleted.md)中所述****。 请注意，30 天的组还原期不可自定义。

如果所还原的组包含文档、SharePoint 站点或其他持久对象，可能需要最多 24 小时才能完全还原该组及其内容。

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>如何检索 Office 365 组到期日期

除了访问面板，用户可以查看组详细信息，包括到期日期和上次续订日期，还可以从 Microsoft 图形 REST API 测试版检索 Office 365 组的到期日期。 过期日期时间作为组属性已在 Microsoft 图形测试版中启用。 可以使用 GET 请求检索它。 有关详细信息，请参阅[此示例](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example)。

> [!NOTE]
> 为了管理访问面板上的组成员身份，需要在 Azure 活动目录组常规设置中将"限制访问访问面板中的组"设置为"否"。

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Office 365 组到期与合法保留邮箱一起使用的方式

组到期并删除后，应用（例如 Planner、站点或 Teams）中删除的组数据将会在 30 天后永久删除，但合法保留的组邮箱将会保留下来并且不会永久删除。 管理员可以使用 Exchange cmdlets 来恢复邮箱以提取数据。

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Office 365 组到期与保留策略一起使用的方式

保留策略是通过安全和合规中心配置的。 如果为 Office 365 组设置了保留策略，组到期并被删除后，组邮箱中的组对话和组站点中的文件将保留在保留容器中，保留的特定天数取决于保留策略中的定义。 到期后，用户无法查看组或其中的内容，但可以通过电子发现恢复站点和邮箱数据。

## <a name="powershell-examples"></a>PowerShell 示例

以下是如何使用 PowerShell cmdlet 配置 Azure AD 组织中 Office 365 组的过期设置的示例：

1. 安装 PowerShell v2.0 模块并在 PowerShell 提示符处登录：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 配置过期设置 使用 New-AzureMSGroup 生命周期策略 cmdlet 将 Azure AD 组织中所有 Office 365 组的生存期设置为 365 天。 没有所有者的 Office 365 组的续订通知将发送到emailaddress@contoso.com''
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 检索现有策略 Get-AzureADMSGroupLifecyclePolicy：此 cmdlet 检索当前已配置的 Office 365 组到期设置。 在此示例中，可以看到：

   - 策略 ID
   - Azure AD 组织中所有 Office 365 组的生存期设置为 365 天
   - 没有所有者的 Office 365 组的续订通知将发送到emailaddress@contoso.com'。
  
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
  
1. 删除现有策略 Remove-AzureADMSGroupLifecyclePolicy：此 cmdlet 会删除 Office 365 组到期设置，但需要策略 ID。 此 cmdlet 禁用 Office 365 组的过期。
  
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
