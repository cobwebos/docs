---
title: 在 Azure AD 权利管理中管理外部用户的访问权限 - Azure Active Directory
description: 了解可以在 Azure Active Directory 权利管理中指定哪些设置来管理外部用户的访问权限。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff683c7c3214be6ae60b5d00d4cd1c2becc32e2
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447090"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中管理外部用户的访问权限

Azure AD 权利管理利用 [Azure AD 企业对企业 (B2B) 解决方案](../external-identities/what-is-b2b.md)与另一目录中的组织外部人员协作。 使用 Azure AD B2B 解决方案时，外部用户会向其主目录进行身份验证，但在你的目录中会有一个表示形式。 可以通过你的目录中的该表示形式为用户分配资源访问权限。

本文介绍了可以指定哪些设置来管理外部用户的访问权限。

## <a name="how-entitlement-management-can-help"></a>权利管理的具体用途

使用 [Azure AD B2B](../external-identities/what-is-b2b.md) 邀请体验时，你必须已经知道要引入到资源目录中并与之合作的外部来宾用户的电子邮件地址。 如果你处理的项目较小或者期限较短，而且你已经知道所有参与者，这会非常有用；但如果你有很多要与之合作的用户，或者参与者会随着时间推移而变化，则较难管理这种情况。  例如，你可能在与另一组织合作，与该组织有一个联系点，但随着时间的推移，该组织中的其他用户也需要访问权限。

使用权利管理，你可以定义一个策略，允许指定组织中的用户自行请求访问包。 你可以指定是否需要批准，还可以指定访问的到期日期。 如果需要批准，你还可以将外部组织中的一个或多个用户邀请到你的目录，将其指定为审批者，因为他们可能知道其组织中的哪些外部用户需要访问权限。 配置访问包后，可以将访问包的链接发送给外部组织的联系人（发起人）。 该联系人可以将其与外部组织中的其他用户共享，这些用户可以使用此链接来请求访问包。 该组织中已被邀请到你的目录的用户也可以使用该链接。

批准请求后，权利管理会为用户预配必要的访问权限。如果用户尚未加入你的目录，则可能还需要邀请该用户。 Azure AD 会自动为其创建 B2B 来宾帐户。 请注意，管理员以前可能已通过设置 [B2B 允许或拒绝列表](../external-identities/allow-deny-list.md)来允许或阻止邀请到其他组织，对允许哪些组织进行协作进行了限制。  如果允许或阻止列表不允许某个用户，则不会邀请该用户。

由于你不希望外部用户的访问权限永远有效，因此请在策略中指定一个过期日期，例如 180 天。 180 天后，如果其访问权限未延长，则权利管理会删除与该访问包关联的所有访问权限。 默认情况下，如果通过权利管理邀请的用户没有其他访问包分配，则当其失去最后一次分配时，系统会阻止其来宾帐户在 30 天内登录，随后会将其删除。 这可以防止不必要帐户的激增。 如以下部分所述，这些设置是可配置的。

## <a name="how-access-works-for-external-users"></a>外部用户访问权限的工作方式

下面的图和步骤概要介绍了如何向外部用户授予对访问包的访问权限。

![显示了外部用户的生命周期的关系图](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 为你想要与之协作的 Azure AD 目录或域[添加连接的组织](entitlement-management-organization.md)。

1. 在目录中创建一个访问包，使其包含应用于[不在目录中的用户](entitlement-management-access-package-create.md#for-users-not-in-your-directory)的策略。

1. 将[“我的访问权限门户”链接](entitlement-management-access-package-settings.md)发送给外部组织的联系人，此联系人可以与其用户共享此链接，以便请求访问包。

1. 外部用户（在此示例中为“请求者 A”）使用“我的访问权限”门户链接来针对访问包[请求访问权限](entitlement-management-request-access.md)。 用户如何登录取决于在连接的组织中定义的目录或域的身份验证类型。

1. 审批者[批准请求](entitlement-management-request-approve.md)（或者由系统自动批准请求）。

1. 请求进入[“正在传递”状态](entitlement-management-process.md)。

1. 使用 B2B 邀请过程，在目录中创建来宾用户帐户（在此示例中为“请求者 A (来宾)”）。 如果定义了[允许列表或拒绝列表](../external-identities/allow-deny-list.md)，则会应用列表设置。

1. 为来宾用户分配对访问包中所有资源的访问权限。 在 Azure AD 中进行更改并将其传播到其他 Microsoft Online Services 或连接的 SaaS 应用程序可能需要一些时间。 有关详细信息，请参阅[应用更改时](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部用户会收到一封指示其访问权限已[传递](entitlement-management-process.md)的电子邮件。

1. 若要访问资源，外部用户可以单击电子邮件中的链接，或者尝试直接访问任何目录资源来完成邀请过程。

1. 根据策略设置，随着时间的推移，外部用户的访问包分配会过期，系统会删除外部用户的访问权限。

1. 根据外部用户设置的生命周期，当外部用户不再有任何访问包分配时，系统会阻止外部用户登录，并从目录中删除来宾用户帐户。

## <a name="settings-for-external-users"></a>外部用户的设置

若要确保组织外部的人员可以请求访问包并获取对这些访问包中的资源的访问权限，应验证某些设置是否已正确配置。

### <a name="enable-catalog-for-external-users"></a>为外部用户启用目录

- 默认情况下，当你创建[新目录](entitlement-management-catalog-create.md)时，会启用新目录以允许外部用户请求目录中的访问包。 确保“为外部用户启用”已设置为“是” 。

    ![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>配置 Azure AD B2B 外部协作设置

- 允许来宾将其他来宾邀请到你的目录意味着来宾邀请可以在权利管理外部进行。 建议将“来宾可邀请”设置为“否” ，只允许正确控制的邀请。
- 如果你使用的是 B2B 允许列表，则必须确保将你需要通过权利管理与之合作的任何域添加到该列表中。 另外，如果你使用的是 B2B 拒绝列表，则必须确保不将你需要与之合作的任何域添加到该列表中。
- 如果为 **所有用户** 创建了权利管理策略 (所有连接的组织 +) 的所有外部用户，并且用户不属于目录中的已连接组织，则在请求包时将自动为其创建连接的组织。 你拥有的任何 B2B 允许或拒绝列表设置优先。 因此，请务必将要包含在此策略中的域添加到允许列表（如果你使用的是允许列表），或将其从拒绝列表中排除（如果你使用的是拒绝列表）。
- 如果你想要创建包含所有 **用户** (所有连接的组织以及) 的所有外部用户的授权管理策略，则必须首先为你的目录启用电子邮件一次性密码身份验证。 有关详细信息，请参阅 [电子邮件一次性密码身份验证 (预览) ](../external-identities/one-time-passcode.md#opting-in-to-the-preview)。
- 有关 Azure AD B2B 外部协作设置的详细信息，请参阅[启用 B2B 外部协作并管理谁可以邀请来宾](../external-identities/delegate-invitations.md)。

    ![Azure AD 外部协作设置](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>查看条件访问策略

- 确保在新来宾用户无法满足的任何条件访问策略中排除来宾，因为这样会阻止他们登录到你的目录。 例如，来宾可能没有已注册的设备，不在已知的位置，并且不想重新注册多重身份验证 (MFA)，因此在条件访问策略中添加这些要求会阻止来宾使用权利管理。 有关详细信息，请参阅 [Azure Active Directory 条件访问中的条件是什么？](../conditional-access/concept-conditional-access-conditions.md)。

    ![Azure AD 条件访问策略的排除设置](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>查看 SharePoint Online 外部共享设置

- 若要在外部用户的访问包中包括 SharePoint Online 站点，请确保将组织级别的外部共享设置为“任何人”（用户不需要登录）或“新的和现有的来宾”（来宾必须登录或提供验证码）。 有关详细信息，请参阅[打开或关闭外部共享](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 如果要限制权利管理外部的任何外部共享，可以将外部共享设置设为“现有来宾”。 这样，只有通过权利管理邀请的新用户才能访问这些站点。 有关详细信息，请参阅[打开或关闭外部共享](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 确保站点级设置启用来宾访问（与前面列出的选项选择相同）。 有关详细信息，请参阅[为站点打开或关闭外部共享](/sharepoint/change-external-sharing-site)。

### <a name="review-your-microsoft-365-group-sharing-settings"></a>查看 Microsoft 365 组共享设置

- 若要在外部用户的访问包中包括 Microsoft 365 组，请确保将“允许用户向组织添加新来宾”设置为“启用”，以允许来宾访问。 有关详细信息，请参阅[管理对 Microsoft 365 组的来宾访问](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access)。

- 如果希望外部用户能够访问与 Microsoft 365 组关联的 SharePoint Online 站点和资源，请确保启用 SharePoint Online 外部共享。 有关详细信息，请参阅[打开或关闭外部共享](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 若要了解如何在 PowerShell 中在目录级别设置 Microsoft 365 组的来宾策略，请参阅[示例：在目录级别配置组的来宾策略](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)。

### <a name="review-your-teams-sharing-settings"></a>查看 Teams 共享设置

- 若要在外部用户的访问包中包括 Teams，请确保将“在 Microsoft Teams 中允许来宾访问”设置为“启用”，以允许来宾访问。 有关详细信息，请参阅[在 Microsoft Teams 管理中心配置来宾访问](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部用户的生命周期

你可以选择当外部用户（已通过正在审批的访问包请求邀请到你的目录的用户）不再有任何访问包分配时将发生什么情况。 如果用户放弃其所有访问包分配，或者其最后一个访问包分配过期，则可能会发生这种情况。 默认情况下，当外部用户不再有任何访问包分配时，系统会阻止其登录到你的目录。 30 天后，系统会从你的目录中删除外部用户的来宾用户帐户。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在“权利管理”部分的左侧菜单中，单击“设置”。********

1. 单击 **“编辑”** 。

    ![用于管理外部用户的生命周期的设置](./media/entitlement-management-external-users/settings-external-users.png)

1. 在“管理外部用户的生命周期”部分中，为外部用户选择不同的设置。

1. 当外部用户失去其对任何访问包的最后一个分配时，如果想要阻止其登录到此目录，请将“阻止外部用户登录到此目录”设置为“是” 。

    > [!NOTE]
    > 如果阻止用户登录到此目录，则用户将无法在此目录中重新请求访问包或请求其他访问权限。 如果用户以后需要请求访问其他访问包，请不要通过配置阻止其登录。

1. 当外部用户失去其对任何访问包的最后一个分配时，如果想要删除其在此目录中的来宾用户帐户，请将“删除外部用户”设置为“是” 。

    > [!NOTE]
    > 权利管理仅删除通过权利管理邀请的帐户。 另请注意，系统会阻止用户登录并从此目录中删除用户，即使已将该用户添加到此目录中不是访问包分配的资源。 如果来宾在接收访问包分配之前已存在于此目录中，则会将其保留。 但是，如果是通过访问包分配邀请的来宾，并且在邀请之后还将其分配给了 OneDrive for Business 或 SharePoint Online 站点，则仍会将其删除。

1. 如果要删除此目录中的来宾用户帐户，可以设置一个天数，该天数过后即可将其删除。 如果要在来宾用户帐户丢失其对任何访问包的最后一个分配时立即将其删除，请将“从此目录中删除外部用户之前需等待的天数”设置为“0”。 

1. 单击“ **保存**”。

## <a name="next-steps"></a>后续步骤

- [添加连接的组织](entitlement-management-organization.md)
- [适用于不在目录中的用户](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [故障排除](entitlement-management-troubleshoot.md)