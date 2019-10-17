---
title: 在 Azure AD 权限管理（预览版）中管理外部用户的访问权限-Azure Active Directory
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
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcf4a0272e21a1fba3cf9adbd9158492e4318578
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452986"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 授权管理（预览版）中为外部用户控制访问权限

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

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

1. 在目录中创建一个访问包，其中包括[不在你的目录中的用户](entitlement-management-access-package-create.md#for-users-not-in-your-directory)的策略。

1. 你将 "[我的访问门户" 链接](entitlement-management-access-package-settings.md)发送到你的外部组织中的联系人，用户可与其用户共享该链接来请求访问包。

1. 外部用户（在本示例中**为请求者 A** ）使用 My access 门户链接来[请求](entitlement-management-request-access.md)访问访问包。

1. 审批者[批准请求](entitlement-management-request-approve.md)（或自动批准请求）。

1. 请求进入[传递状态](entitlement-management-process.md)。

1. 使用 B2B 邀请过程，将在目录中创建来宾用户帐户（在本示例中为**请求者 a （guest）** ）。 如果定义了[允许列表或拒绝列表](../b2b/allow-deny-list.md)，将应用列表设置。

1. 向来宾用户分配对访问包中所有资源的访问权限。 在 Azure AD 和其他 Microsoft Online Services 或连接的 SaaS 应用程序中进行更改时，可能需要一段时间。 有关详细信息，请参阅[何时应用更改](entitlement-management-access-package-resources.md#when-changes-are-applied)。

1. 外部用户将收到一封电子邮件，指示其访问权限已[送达](entitlement-management-process.md)。

1. 若要访问资源，外部用户可以单击电子邮件中的链接，或者尝试直接访问任何目录资源来完成邀请过程。

1. 根据策略设置的时间，外部用户的访问包分配将过期，并且删除外部用户的访问权限。

1. 根据外部用户设置的生命周期，当外部用户不再具有任何访问包分配时，会阻止外部用户登录，并从目录中删除来宾用户帐户。

## <a name="manage-the-lifecycle-of-external-users"></a>管理外部用户的生命周期

你可以选择在通过审批访问包请求而被邀请到你的目录的外部用户，不再具有任何访问包分配时所发生的情况。 如果用户让给其所有访问包分配，或者其上次访问包分配过期，则可能会发生这种情况。 默认情况下，当外部用户不再具有任何访问包分配时，会阻止它们登录到你的目录。 30天后，将从你的目录中删除其来宾用户帐户。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单的 "**权利管理**" 部分中，单击 "**设置**"。

1. 单击“编辑”。

    ![用于管理外部用户生命周期的设置](./media/entitlement-management-external-users/settings-external-users.png)

1. 在 "**管理外部用户的生命周期**" 部分中，选择外部用户的不同设置。

1. 当外部用户失去其对任何访问包的最后一次分配时，如果想要阻止它们登录到此目录，请将 "**阻止外部用户登录到此目录**" 设置为 **"是"** 。

1. 当外部用户丢失其对任何访问包的最后一次分配时，如果你想要在目录中删除其来宾用户帐户，请将 "**删除外部用户**" 设置为 **"是"** 。

    > [!NOTE]
    > 权利管理仅删除通过授权管理进行邀请的帐户。 另请注意，将阻止用户登录并从目录中删除用户，即使该用户已添加到目录中未访问包分配的资源。 如果在接收访问包分配之前，来宾在目录中存在，则会保留。 但是，如果来宾是通过访问包分配进行邀请的，并且在收到邀请之后还将其分配给 OneDrive for business 或 SharePoint Online 站点，则它们仍会被删除。

1. 如果要删除目录中的来宾用户帐户，可以设置删除之前的天数。 如果要在来宾用户帐户丢失其最后一次分配到任何访问包时立即将其删除，请设置将**此目录中的外部用户删除到0前的天数**。

1. 单击“保存”。

## <a name="enable-a-catalog-for-external-users"></a>为外部用户启用目录

创建[新目录](entitlement-management-catalog-create.md)时，可以使用一个设置来使外部目录中的用户可以在目录中请求访问包。 如果你不希望外部用户有权请求目录中的访问包，请将 "**为外部用户启用**" 设置为 "**否**"。

**必备角色：** 全局管理员、用户管理员或目录所有者

![新建目录窗格](./media/entitlement-management-shared/new-catalog.png)

你还可以在创建目录后更改此设置。

![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>后续步骤

- [对于不在你的目录中的用户](entitlement-management-access-package-create.md#for-users-not-in-your-directory)
- [创建和管理资源的目录](entitlement-management-catalog-create.md)
- [委托和角色](entitlement-management-delegate.md)
