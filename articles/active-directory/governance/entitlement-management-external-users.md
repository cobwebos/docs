---
title: 在 Azure AD 授权管理中管理外部用户的访问 - Azure 活动目录
description: 了解为在 Azure 活动目录授权管理中管理外部用户的访问而指定的设置。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128697"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中管理外部用户的访问权限

Azure AD 授权管理利用[Azure AD 企业对企业 （B2B）](../b2b/what-is-b2b.md)与另一个目录中的组织外部人员协作。 使用 Azure AD B2B，外部用户对其主目录进行身份验证，但在目录中具有表示形式。 目录中的表示形式使用户能够分配对资源的访问。

本文介绍了为控制外部用户的访问而可以指定的设置。

## <a name="how-entitlement-management-can-help"></a>授权管理如何提供帮助

使用[Azure AD B2B](../b2b/what-is-b2b.md)邀请体验时，必须已经知道要引入资源目录并处理的外部来宾用户的电子邮件地址。 当您正在处理一个较小的或短期的项目，并且您已经知道所有参与者时，这非常有效，但是，如果您有大量要使用的用户，或者参与者随时间而变化，则很难管理。  例如，您可能正在与其他组织合作，并且与该组织有一个联系点，但随着时间的推移，该组织的其他用户也需要访问权限。

使用授权管理，您可以定义一个策略，允许指定组织的用户能够自行请求访问包。 您可以指定是否需要批准以及访问到期日期。 如果需要批准，您还可以邀请一个或多个用户从外部组织到您的目录，并将他们指定为审批者 -因为他们可能知道组织中哪些外部用户需要访问。 配置访问包后，可以将访问包的链接发送给外部组织的联系人（发起人）。 该联系人可以与外部组织中的其他用户共享，并且他们可以使用此链接请求访问包。 已被邀请加入目录的来自该组织的用户也可以使用该链接。

请求获得批准后，授权管理将为用户提供必要的访问权限，其中可能包括邀请用户（如果用户尚未在目录中）。 Azure AD 将自动为其创建 B2B 来宾帐户。 请注意，管理员以前可能通过设置[B2B 允许或拒绝列表](../b2b/allow-deny-list.md)来允许或阻止对其他组织的邀请来限制哪些组织允许协作。  如果允许或阻止列表不允许用户，则不会邀请他们。

由于不希望外部用户的访问永远持续，因此在策略中指定到期日期，例如 180 天。 180 天后，如果访问未扩展，授权管理将删除与该访问包关联的所有访问权限。 默认情况下，如果通过授权管理邀请的用户没有其他访问包分配，则当他们失去上次分配时，其来宾帐户将被阻止登录 30 天，然后被删除。 这可以防止不必要的帐户激增。 如以下各节所述，这些设置是可配置的。

## <a name="how-access-works-for-external-users"></a>访问对外部用户的工作原理

下图和步骤概述了如何授予外部用户访问包访问权限。

![显示外部用户生命周期的图表](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 为要协作的 Azure AD 目录或域[添加已连接的组织](entitlement-management-organization.md)。

1. 在目录中创建一个访问包，其中包含[针对不在目录中的用户](entitlement-management-access-package-create.md#for-users-not-in-your-directory)的策略。

1. 您将["我的访问"门户链接](entitlement-management-access-package-settings.md)发送给外部组织的联系人，用户可以与其用户共享该链接以请求访问包。

1. 外部用户（本示例中**的请求器 A）** 使用"我的访问"门户链接[请求访问](entitlement-management-request-access.md)包。 用户登录方式取决于连接组织中定义的目录或域的身份验证类型。

1. 审批人[批准请求](entitlement-management-request-approve.md)（或请求是自动批准的）。

1. 请求进入[传递状态](entitlement-management-process.md)。

1. 使用 B2B 邀请过程，在目录中创建来宾用户帐户（本示例中的请求**器 A（来宾）。** 如果定义了[允许列表或拒绝列表](../b2b/allow-deny-list.md)，将应用列表设置。

1. 来宾用户被分配对访问包中的所有资源的访问权限。 在 Azure AD 和其他 Microsoft 在线服务或连接的 SaaS 应用程序中进行更改可能需要一些时间。 有关详细信息，请参阅[何时应用更改](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部用户收到一封电子邮件，指示其访问权限已[送达](entitlement-management-process.md)。

1. 要访问资源，外部用户可以单击电子邮件中的链接，也可以尝试直接访问任何目录资源以完成邀请过程。

1. 根据策略设置，随着时间的推移，外部用户的访问包分配将过期，并删除外部用户的访问权限。

1. 根据外部用户设置的生命周期，当外部用户不再具有任何访问包分配时，外部用户将被阻止登录，来宾用户帐户将从目录中删除。

## <a name="settings-for-external-users"></a>外部用户的设置

为了确保组织外部的人员可以请求访问包并访问这些访问包中的资源，应验证某些设置是否正确配置。

### <a name="enable-catalog-for-external-users"></a>为外部用户启用目录

- 默认情况下，当您[创建新目录](entitlement-management-catalog-create.md)时，它启用允许外部用户请求目录中的访问包。 确保**为外部用户启用**设置为 **"是**"。

    ![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>配置 Azure AD B2B 外部协作设置

- 允许客人邀请其他客人加入您的目录意味着客人邀请可能发生在权利管理之外。 我们建议设置 **"来宾可以邀请**到 **""** 仅允许正确管理的邀请。
- 如果使用 B2B 允许列表，必须确保要与使用授权管理合作的任何域都添加到列表中。 或者，如果您使用的是 B2B 拒绝列表，则必须确保要与任何要合作的域不会添加到列表中。
- 如果为**所有用户**（所有连接的组织 + 任何新的外部用户）创建授权管理策略，则任何 B2B 允许或拒绝列表设置将优先。 因此，请确保将要在此策略中包括的域包含在允许列表中（如果您正在使用）中，并且如果您正在使用拒绝列表，则将其从拒绝列表中排除。
- 如果要创建包含**所有用户**（所有连接的组织 + 任何新外部用户）的权利管理策略，必须首先为目录启用电子邮件一次性密码身份验证。 有关详细信息，请参阅[电子邮件一次性密码身份验证（预览）。](../b2b/one-time-passcode.md#opting-in-to-the-preview)
- 有关 Azure AD B2B 外部协作设置的详细信息，请参阅[启用 B2B 外部协作并管理谁可以邀请来宾](../b2b/delegate-invitations.md)。

    ![Azure AD 外部协作设置](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>查看条件访问策略

- 请确保将来宾排除在新来宾用户无法满足的任何条件访问策略中，因为这将阻止他们登录到您的目录。 例如，来宾可能没有注册设备，不在已知位置，并且不想重新注册多重身份验证 （MFA），因此在条件访问策略中添加这些要求将阻止来宾使用授权管理。 有关详细信息，请参阅[Azure 活动目录条件访问中的条件是什么？](../conditional-access/concept-conditional-access-conditions.md)

    ![Azure AD 条件访问策略排除设置](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>查看 SharePoint 在线外部共享设置

- 如果要在外部用户的访问包中包含 SharePoint Online 网站，请确保组织级外部共享设置设置为 **"任何人**"（用户不需要登录）或 **"新建"和"现有来宾**"（来宾必须登录或提供验证码）。 有关详细信息，请参阅[打开或关闭外部共享](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 如果要限制授权管理之外的任何外部共享，可以将外部共享设置设置为**现有来宾**。 然后，只有通过授权管理邀请的新用户才能访问这些网站。 有关详细信息，请参阅[打开或关闭外部共享](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 确保站点级设置启用来宾访问（与前面列出的选项选择相同）。 有关详细信息，请参阅[打开或关闭站点的外部共享](https://docs.microsoft.com/sharepoint/change-external-sharing-site)。

### <a name="review-your-office-365-group-sharing-settings"></a>查看 Office 365 组共享设置

- 如果要在外部用户的访问包中包括 Office 365 组，请确保 **"让用户向组织添加新来宾**"设置为 **"打开**"以允许来宾访问。 有关详细信息，请参阅[管理来宾对 Office 365 组的访问](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)。

- 如果希望外部用户能够访问 SharePoint Online 网站以及与 Office 365 组关联的资源，请确保打开 SharePoint 联机外部共享。 有关详细信息，请参阅[打开或关闭外部共享](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)。

- 有关如何在 PowerShell 中的目录级别为 Office 365 组设置来宾策略的信息，请参阅[示例：在目录级别为组配置来宾策略](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)。

### <a name="review-your-teams-sharing-settings"></a>查看您的团队共享设置

- 如果要在外部用户的访问包中包含 Teams，请确保 Microsoft Teams**中的"允许来宾访问**"设置为 **"打开**"以允许访客访问。 有关详细信息，请参阅在[Microsoft 团队管理中心配置来宾访问](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部用户的生命周期

您可以选择外部用户（通过受批准的访问包请求受邀进入目录）时发生的情况，该请求不再具有任何访问包分配。 如果用户放弃其所有访问包分配，或者其上次访问包分配过期，则可能发生这种情况。 默认情况下，当外部用户不再具有任何访问包分配时，他们将被阻止登录到您的目录。 30 天后，他们的来宾用户帐户将从您的目录中删除。

**先决条件角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在“权利管理”部分的左侧菜单中，单击“设置”。********

1. 单击 **“编辑”**。

    ![管理外部用户生命周期的设置](./media/entitlement-management-external-users/settings-external-users.png)

1. 在 **"管理外部用户生命周期**"部分中，为外部用户选择不同的设置。

1. 一旦外部用户失去了对任何访问包的最后分配，如果要阻止他们登录到此目录，请将**阻止外部用户登录到此目录**设置为 **"是**"。

    > [!NOTE]
    > 如果用户被阻止登录到此目录，则用户将无法重新请求访问包或请求此目录中的其他访问权限。 如果随后需要请求访问其他访问包，则不要配置阻止他们登录。

1. 一旦外部用户失去对任何访问包的最后分配，如果要删除其在此目录中的来宾用户帐户，请将 **"删除外部用户"** 设置为 **"是**"。

    > [!NOTE]
    > 授权管理仅删除通过授权管理邀请的帐户。 此外，请注意，即使该用户已添加到此目录中未访问包分配的资源，用户也会被阻止登录到并将其从此目录中删除。 如果在接收访问包分配之前来宾在此目录中存在，则来宾将保留。 但是，如果通过访问包分配邀请该来宾，并且被邀请后也分配到 OneDrive 商务或 SharePoint Online 网站，他们仍将被删除。

1. 如果要删除此目录中的来宾用户帐户，可以设置删除该帐户的天数。 如果要在来宾用户帐户丢失对任何访问包的最后分配后立即删除该帐户，请将**从此目录中删除外部用户之前的天数**设置为**0**。

1. 单击“保存”。****

## <a name="next-steps"></a>后续步骤

- [添加连接的组织](entitlement-management-organization.md)
- [适用于不在目录中的用户](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [疑难解答](entitlement-management-troubleshoot.md)
