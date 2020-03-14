---
title: 为外部用户管理对 Azure AD 权限管理的访问权限-Azure Active Directory
description: 了解你可以指定的设置，以控制对 Azure Active Directory 授权管理中外部用户的访问。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7e4837aaf65b0df28ea4a07fb485948309bc7c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261822"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>为外部用户管理对 Azure AD 权限管理的访问权限

Azure AD 的权利管理利用[Azure AD 企业到企业（B2B）](../b2b/what-is-b2b.md)与组织外的其他目录中的人员进行协作。 使用 Azure AD B2B 时，外部用户会对其主目录进行身份验证，但在目录中具有表示形式。 目录中的表示形式允许用户分配对资源的访问权限。

本文介绍可指定用于管理外部用户访问权限的设置。

## <a name="how-entitlement-management-can-help"></a>权限管理如何帮助

使用[AZURE AD B2B](../b2b/what-is-b2b.md)邀请体验时，必须知道要引入资源目录并使用的外部来宾用户的电子邮件地址。 当您处理的项目较小或短期，而且您已经了解了所有参与者时，这非常有用，但如果您有很多用户要使用，或者参与者随着时间推移而发生变化，则很难管理这种情况。  例如，你可能与其他组织合作，与该组织有一个联系点，但随着时间的推移，该组织中的其他用户也需要访问权限。

使用权限管理，你可以定义一个策略，该策略允许你指定的组织中的用户能够自行请求访问包。 你可以指定是否需要批准和访问的到期日期。 如果需要批准，还可以将外部组织中的一个或多个用户邀请到你的目录，并将其指定为审批者，因为他们可能知道组织中的哪些外部用户需要访问权限。 配置访问包后，可以将访问包的链接发送给外部组织的联系人（发起方）。 该联系人可以与外部组织中的其他用户共享，他们可以使用此链接请求访问包。 该组织中已邀请到你的目录的用户也可以使用该链接。

批准请求后，授权管理会为用户预配必要的访问权限，如果用户尚未加入你的目录，则可能包括邀请用户。 Azure AD 会自动为他们创建 B2B 来宾帐户。 请注意，管理员可能已通过设置[B2B 允许或拒绝列表](../b2b/allow-deny-list.md)来允许或阻止其他组织的邀请，因而限制了允许协作的组织。  如果允许或阻止列表不允许用户，则不会邀请用户。

由于你不希望外部用户访问最后一次，因此请在策略中指定一个过期日期，如180天。 180天后，如果其访问权限未扩展，则权限管理将删除与该访问包关联的所有访问权限。 默认情况下，如果通过授权管理的用户没有其他访问包分配，则当他们失去最后一次分配时，将阻止其来宾帐户在30天内登录，并随后将其删除。 这可以防止不必要帐户的增加。 如以下部分所述，这些设置是可配置的。

## <a name="how-access-works-for-external-users"></a>外部用户访问权限的工作方式

以下关系图和步骤概述了如何向外部用户授予访问包访问权限。

![显示外部用户的生命周期的关系图](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 为要与之协作的 Azure AD 目录或域[添加连接的组织](entitlement-management-organization.md)。

1. 在目录中创建一个访问包，其中包括[不在你的目录中的用户](entitlement-management-access-package-create.md#for-users-not-in-your-directory)的策略。

1. 你将 "[我的访问门户" 链接](entitlement-management-access-package-settings.md)发送到你的外部组织中的联系人，用户可与其用户共享该链接来请求访问包。

1. 外部用户（在本示例中**为请求者 A** ）使用 My access 门户链接来[请求](entitlement-management-request-access.md)访问访问包。 用户登录的方式取决于在连接的组织中定义的目录或域的身份验证类型。

1. 审批者[批准请求](entitlement-management-request-approve.md)（或自动批准请求）。

1. 请求进入[传递状态](entitlement-management-process.md)。

1. 使用 B2B 邀请过程，将在目录中创建来宾用户帐户（在本示例中为**请求者 a （guest）** ）。 如果定义了[允许列表或拒绝列表](../b2b/allow-deny-list.md)，将应用列表设置。

1. 向来宾用户分配对访问包中所有资源的访问权限。 在 Azure AD 和其他 Microsoft Online Services 或连接的 SaaS 应用程序中进行更改时，可能需要一段时间。 有关详细信息，请参阅[何时应用更改](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部用户将收到一封电子邮件，指示其访问权限已[送达](entitlement-management-process.md)。

1. 若要访问资源，外部用户可以单击电子邮件中的链接，或者尝试直接访问任何目录资源来完成邀请过程。

1. 根据策略设置的时间，外部用户的访问包分配将过期，并且删除外部用户的访问权限。

1. 根据外部用户设置的生命周期，当外部用户不再具有任何访问包分配时，会阻止外部用户登录，并从目录中删除来宾用户帐户。

## <a name="settings-for-external-users"></a>外部用户的设置

若要确保组织外部的人员可以请求访问包并获取对这些访问包中的资源的访问权限，你应验证一些设置是否正确配置。

### <a name="enable-catalog-for-external-users"></a>为外部用户启用目录

- 默认情况下，当你创建[新目录](entitlement-management-catalog-create.md)时，它将启用以允许外部用户在目录中请求访问包。 确保 "**为外部用户启用**" 设置为 **"是"** 。

    ![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>配置 Azure AD B2B 外部协作设置

- 允许来宾邀请其他来宾到你的目录，这意味着来宾邀请可以在权限管理之外发生。 建议将**来宾**设置为 "**无**"，以仅允许正确控制的邀请。
- 如果你使用的是 B2B 允许列表，则必须确保将使用权限管理的任何域添加到该列表中。 或者，如果您使用的是 B2B 拒绝列表，则必须确保您要使用的任何域都不会添加到该列表中。
- 如果为**所有用户**（所有连接的组织 + 任何新的外部用户）创建了一个授权管理策略，则你拥有的任何 B2B 允许或拒绝列表设置将优先。 因此，请务必将要包含在此策略中的域添加到允许列表（如果你使用的是 "拒绝列表"），并将其从拒绝列表中排除。
- 如果你想要创建包含**所有用户**（所有连接的组织 + 任何新的外部用户）的授权管理策略，你必须首先为你的目录启用电子邮件一次性密码身份验证。 有关详细信息，请参阅[电子邮件一次性密码身份验证（预览版）](../b2b/one-time-passcode.md#opting-in-to-the-preview)。
- 有关 Azure AD B2B 外部协作设置的详细信息，请参阅[启用 b2b 外部协作和管理可以邀请来宾的人员](../b2b/delegate-invitations.md)。

    ![Azure AD 外部协作设置](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>查看条件访问策略

- 请确保从新的来宾用户无法满足的任何条件性访问策略中排除来宾，因为这样会阻止用户登录到你的目录。 例如，来宾可能没有已注册的设备，不在已知的位置，并且不想重新注册多重身份验证（MFA），因此在条件性访问策略中添加这些要求会阻止来宾使用权利层. 有关详细信息，请参阅[什么是条件性访问中的条件 Azure Active Directory？](../conditional-access/concept-conditional-access-conditions.md)。

    ![Azure AD 条件访问策略排除设置](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>查看 SharePoint Online 外部共享设置

- 如果要在访问包中为外部用户包括 SharePoint Online 站点，请确保将组织级别的外部共享设置设置为 "**任何人**" （用户不需要登录）或**新的和现有的来宾**（来宾必须登录或提供验证码）。 有关详细信息，请参阅[打开或关闭外部共享](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 如果要限制权限管理之外的任何外部共享，则可以将外部共享设置设置为**现有来宾**。 然后，只有通过授权管理的新用户才能访问这些站点。 有关详细信息，请参阅[打开或关闭外部共享](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 请确保站点级设置启用来宾访问（与前面列出的选项相同）。 有关详细信息，请参阅[为站点启用或禁用外部共享](https://docs.microsoft.com/sharepoint/change-external-sharing-site)。

### <a name="review-your-office-365-group-sharing-settings"></a>查看 Office 365 组共享设置

- 如果你想要在你的外部用户的访问包中包含 Office 365 组，请确保将 "**允许用户向组织添加新来宾"** 设置为 **"开**" 以允许来宾访问。 有关详细信息，请参阅[管理对 Office 365 组的来宾访问权限](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)。

- 如果希望外部用户能够访问与 Office 365 组关联的 SharePoint Online 网站和资源，请确保启用 SharePoint Online 外部共享。 有关详细信息，请参阅[打开或关闭外部共享](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 有关如何在 PowerShell 中的目录级别设置 Office 365 组的来宾策略的信息，请参阅[示例：在目录级别为组配置来宾策略](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)。

### <a name="review-your-teams-sharing-settings"></a>查看团队共享设置

- 如果要将团队添加到外部用户的访问包中，请确保将 "**允许在 Microsoft 团队中进行来宾访问**" 设置为 **"开**" 以允许来宾访问。 有关详细信息，请参阅[Microsoft 团队管理中心中的配置来宾访问权限](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部用户的生命周期

你可以选择在通过审批访问包请求而被邀请到你的目录的外部用户，不再具有任何访问包分配时所发生的情况。 如果用户让给其所有访问包分配，或者其上次访问包分配过期，则可能会发生这种情况。 默认情况下，当外部用户不再具有任何访问包分配时，会阻止它们登录到你的目录。 30天后，将从你的目录中删除其来宾用户帐户。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单的 "**权利管理**" 部分中，单击 "**设置**"。

1. 单击 **“编辑”** 。

    ![用于管理外部用户生命周期的设置](./media/entitlement-management-external-users/settings-external-users.png)

1. 在 "**管理外部用户的生命周期**" 部分中，选择外部用户的不同设置。

1. 当外部用户失去其对任何访问包的最后一次分配时，如果想要阻止它们登录到此目录，请将 "**阻止外部用户登录到此目录**" 设置为 **"是"** 。

    > [!NOTE]
    > 如果阻止用户登录到此目录，则用户将无法在此目录中重新请求访问包或请求其他访问权限。 如果以后需要请求访问其他访问包，请不要配置阻止它们登录。

1. 当外部用户失去其对任何访问包的最后一次分配时，如果你想要在此目录中删除其来宾用户帐户，请将 "**删除外部用户**" 设置为 **"是"** 。

    > [!NOTE]
    > 权利管理仅删除通过授权管理进行邀请的帐户。 另请注意，将阻止用户登录并从该目录中删除该用户，即使该用户已添加到此目录中未访问包分配的资源。 如果在接收访问包分配之前来宾在此目录中存在，则会保留。 但是，如果来宾是通过访问包分配进行邀请的，并且在收到邀请之后还将其分配给 OneDrive for business 或 SharePoint Online 站点，则它们仍会被删除。

1. 如果要删除此目录中的来宾用户帐户，可以设置删除之前的天数。 如果要在来宾用户帐户丢失其最后一次分配到任何访问包时立即将其删除，请设置将**此目录中的外部用户删除到0前的天数**。

1. 单击“ **保存**”。

## <a name="next-steps"></a>后续步骤

- [添加连接的组织](entitlement-management-organization.md)
- [对于不在你的目录中的用户](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [故障排除](entitlement-management-troubleshoot.md)
