---
title: 在 Azure AD 权利管理（预览版）中编辑和管理现有的访问包 - Azure Active Directory
description: 了解如何在 Azure Active Directory 权利管理（预览版）中编辑和管理现有的访问包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/26/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a79cf166025ced6cb08d2f9e24801ea498fdc1c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326383"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 权利管理（预览版）中编辑和管理现有的访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用访问包可以一次性设置好用于在访问包的生命周期内自动管理访问权限的资源和策略。 访问包管理者随时可以更改访问包中的资源，而无需考虑如何预配用户对新资源的访问权限，或者从以前的资源中删除用户的访问权限。 此外，随时可以更新策略，但是，策略更改只会影响新的访问权限。

本文介绍如何编辑和管理现有的访问包。

## <a name="add-resource-roles"></a>添加资源角色

资源角色是与资源关联的权限集合。 为用户提供可请求的资源的方式是将资源角色添加到访问包。 您可以为组、团队、应用程序和 SharePoint 站点添加资源角色。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在左侧菜单中单击“资源角色”。

1. 单击“添加资源角色”打开“将资源角色添加到访问包”页。

    ![访问包 - 添加资源角色](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 根据你是要添加组、团队、应用程序还是 SharePoint 站点，请执行下列资源角色部分之一中的步骤。

### <a name="add-a-group-or-team-resource-role"></a>添加组或团队资源角色

当分配了访问包时，可以让权利管理自动将用户添加到组或 Microsoft 团队。 

- 如果组或团队是访问包的一部分，并且将用户分配到该访问包，则会将该用户添加到该组或团队（如果尚未存在）。
- 当用户的访问包分配过期时，它们将从组或团队中删除，除非它们当前已分配给另一个访问包，其中包含相同的组或团队。

你可以选择任何[Azure AD 安全组或 Office 365 组](../fundamentals/active-directory-groups-create-azure-portal.md)。  管理员可将任何组添加到目录；如果目录所有者是组的所有者，则他们可将任何组添加到该目录中。 选择组时，请记住以下 Azure AD 约束：

- 当用户（包括来宾）作为成员添加到组或团队时，他们可以查看该组或团队的所有其他成员。
- Azure AD 无法更改使用 Azure AD Connect 从 Windows Server Active Directory 同步的组的成员身份，或在 Exchange Online 中作为通讯组创建的组的成员身份。  
- 无法通过添加或删除成员来更新动态组的成员身份，因此，不适合在权利管理中使用动态组成员身份。

有关详细信息，请参阅[比较组](/office365/admin/create-groups/compare-groups)和[Office 365 组和 Microsoft 团队](/microsoftteams/office-365-groups)。

1. 在 "**将资源角色添加到访问包**" 页面上，单击 "**组和团队**" 以打开 "选择组" 窗格。

1. 选择要包括在访问包中的组和团队。

    ![访问包 - 添加资源角色 - 选择组](./media/entitlement-management-access-package-edit/group-select.png)

1. 单击“选择”。

    选择组或团队后，"**子类型**" 列将列出以下子类型之一：

    |  |  |
    | --- | --- |
    | 安全性 | 用于授予对资源的访问权限。 |
    | 分发 | 用于向一组人员发送通知。 |
    | O365 | 不是团队启用的 Office 365 组。 用于在公司内部和外部用户之间的协作。 |
    | 团队 | 启用了团队的 Office 365 组。 用于在公司内部和外部用户之间的协作。 |

1. 在“角色”列表中，选择“所有者”或“成员”。

    我们通常会选择“成员”角色。 如果选择“所有者”角色，则允许用户添加或删除其他成员或所有者。

    ![访问包-添加组或团队的资源角色](./media/entitlement-management-access-package-edit/group-role.png)

1. 单击“添加”。

    添加了对访问包的现有分配的任何用户将自动成为该组或团队的成员。

### <a name="add-an-application-resource-role"></a>添加应用程序资源角色

为用户分配访问包后，可让 Azure AD 自动为用户分配对 Azure AD 企业应用程序（包括 SaaS 应用程序，以及联合到 Azure AD 的组织应用程序）的访问权限。 对于通过联合单一登录与 Azure AD 相集成的应用程序，Azure AD 将为分配给该应用程序的用户颁发联合身份验证令牌。

应用程序可以有多个角色。 将某个应用程序添加到访问包时，如果该应用程序有多个角色，则你需要为这些用户指定相应的角色。  如果要开发应用程序，可以在如何[配置 SAML 令牌中颁发的角色声明](../develop/active-directory-enterprise-app-role-management.md)一文中阅读有关如何向应用程序提供这些角色的详细信息。

一旦应用程序角色成为访问包的一部分：

- 为某个用户分配该访问包时，该用户将添加到该应用程序角色（如果该用户没有该角色）。
- 当用户的访问包分配过期时，他们的访问权限将从应用程序中删除，除非他们已分配到包含该应用程序角色的其他访问包。

选择应用程序时，请注意以下事项：

- 应用程序还可能已将组分配到其角色。  可以选择添加组来取代访问包中的应用程序角色，但是，用户在“我的访问权限”门户中看不到作为访问包的一部分的应用程序。

1. 在“将资源角色添加到访问包”页上，单击“应用程序”打开“选择应用程序”窗格。

1. 选择要包含在访问包中的应用程序。

    ![访问包 - 添加资源角色 - 选择应用程序](./media/entitlement-management-access-package-edit/application-select.png)

1. 单击“选择”。

1. 在“角色”列表中，选择应用程序角色。

    ![访问包 - 为应用程序添加资源角色](./media/entitlement-management-access-package-edit/application-role.png)

1. 单击“添加”。

    添加此应用程序时，在访问包中具有现有分配的任何用户将自动获得此应用程序的访问权限。

### <a name="add-a-sharepoint-site-resource-role"></a>添加 SharePoint 站点资源角色

为用户分配访问包时，Azure AD 可自动为用户分配对 SharePoint Online 站点或 SharePoint Online 站点集合的访问权限。

1. 在“将资源角色添加到访问包”页上，单击“SharePoint 站点”打开“选择 SharePoint Online 站点”窗格。

1. 选择要包含在访问包中的 SharePoint Online 站点。

    ![访问包 - 添加资源角色 - 选择 SharePoint Online 站点](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. 单击“选择”。

1. 在“角色”列表中，选择 SharePoint Online 站点角色。

    ![访问包 - 为 SharePoint Online 站点添加资源角色](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. 单击“添加”。

    添加此 SharePoint Online 站点时，在访问包中具有现有分配的任何用户将自动获得此 SharePoint Online 站点的访问权限。

## <a name="remove-resource-roles"></a>删除资源角色

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在左侧菜单中单击“资源角色”。

1. 在资源角色列表中，找到要删除的资源角色。

1. 依次单击省略号 ( **...** )、“删除资源角色”。

    删除此资源角色后，在访问包中具有现有分配的任何用户对此资源角色的访问权限将自动吊销。

## <a name="add-a-new-policy"></a>添加新策略

指定谁可以请求访问包的方式是创建策略。 如果希望允许使用不同的审批和过期设置为不同的用户授予分配，可为单个访问包创建多个策略。 单个策略不可用于将内部和外部用户分配到同一个访问包。 但是，可在同一个访问包中创建两个策略 - 一个用于内部用户，一个用于外部用户。 如果对某个用户应用了多个策略，当用户请求选择要分配到的策略时，系统会提示他们。

下图显示了为现有访问包创建策略的概要过程。

![创建策略的过程](./media/entitlement-management-access-package-edit/policy-process.png)

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 依次单击“策略”、“添加策略”。

1. 键入策略的名称和说明。

    ![创建策略并输入名称和说明](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. 根据你对“可以请求访问的用户”所做的选择，执行以下某个策略部分中的步骤。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>编辑现有策略

随时可以编辑策略。 如果更改了策略的过期日期，已处于“等待审批”或“已审批”状态的请求的过期日期不会更改。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 单击“策略”，然后单击要编辑的策略。

    页面底部会打开“策略详细信息”窗格。

    ![访问包 -“策略详细信息”窗格](./media/entitlement-management-access-package-edit/policy-details.png)

1. 单击“编辑”以编辑策略。

    ![访问包 - 编辑策略](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 完成后，单击“更新”。

## <a name="directly-assign-a-user"></a>直接分配用户

在某些情况下，你可能希望直接将特定的用户分配到访问包，使其不必要完成请求访问包的过程。 若要直接分配用户，访问包必须有一个允许管理员直接分配的策略。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在左侧菜单中单击“分配”。

1. 单击“新建分配”打开“将用户添加到访问包”。

    ![分配 - 将用户添加到访问包](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. 单击“添加用户”，选择要将访问包分配到的用户。

1. 在“选择策略”列表中，选择具有 [无(仅限管理员直接分配)](#policy-none-administrator-direct-assignments-only) 设置的策略。

    如果此访问包没有此类策略，你可以单击“创建新策略”添加一个策略。

1. 设置所选用户的分配开始与结束日期和时间。 如果未提供结束日期，将使用策略的过期设置。

1. （可选）为直接分配提供理由，以保留记录。

1. 单击“添加”将所选用户直接分配到访问包。

    片刻之后，单击“刷新”查看“分配”列表中的用户。

## <a name="view-who-has-an-assignment"></a>查看谁具有分配

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 单击“分配”查看活动分配的列表。

1. 单击特定的分配可查看更多详细信息。

1. 若要查看并非所有资源角色都已未正确预配的分配列表，请单击筛选器状态，然后选择“正在传递”。

    在“请求”页上找到用户的对应请求，可以查看有关传递错误的更多详细信息。

1. 若要查看已过期的分配，请单击筛选器状态，然后选择“已过期”。

1. 若要下载筛选列表的 CSV 文件，请单击“下载”。

## <a name="view-requests"></a>查看请求

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 单击“请求”。

1. 单击特定的请求可查看更多详细信息。

## <a name="view-a-requests-delivery-errors"></a>查看请求的传递错误

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 单击“请求”。

1. 选择要查看的请求。

    如果该请求发生了任何传递错误，则请求状态将是“未传递”，子状态将是“已部分传递”。

    如果发生了任何传递错误，请求详细信息窗格中会显示传递错误计数。

1. 单击计数可查看该请求的所有传递错误。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

只能取消尚未传递的挂起请求。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 单击“请求”。

1. 单击要取消的请求

1. 在请求详细信息窗格中，单击“取消请求”。

## <a name="copy-my-access-portal-link"></a>复制“我的访问权限”门户链接

目录中的大多数用户都可以登录到“我的访问权限”门户，并自动查看他们可以请求的访问包列表。 但是，对于尚未加入你的目录的外部企业合作伙伴用户，需要向他们发送一个用于请求访问包的链接。 

将整个 "我的访问门户" 链接发送到内部业务合作伙伴时，必须将其复制到一起，这一点很重要。 这可确保合作伙伴能够访问你的目录门户来发出请求。 

该链接将以 "myaccess" 开头，包含目录提示，并以访问包 id 结尾。请确保该链接包括以下所有内容：

 `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

只要为外部用户启用了访问包，并且为外部用户的目录创建了相应的策略，则外部用户就能使用“我的访问权限”门户链接来请求访问包。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在“概述”页上，复制“我的访问权限门户链接”。

    ![访问包概述 - 我的访问权限门户链接](./media/entitlement-management-shared/my-access-portal-link.png)

1. 通过电子邮件或其他方式将该链接发送给外部企业合作伙伴。 他们可与其用户共享该链接，以请求访问包。

## <a name="change-the-hidden-setting"></a>更改隐藏设置

默认情况下，访问包是可发现的。 这意味着，如果策略允许用户请求访问包，则他们自动会看到该访问包列在“我的访问权限”门户中。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在“概述”页上单击“编辑”。

1. 设置“隐藏”设置。

    如果设置为“否”，则该访问包将列在用户的“我的访问权限”门户中。

    如果设置为“是”，则该访问包不会列在用户的“我的访问权限”门户中。 仅当用户已获得访问包的“我的访问权限”门户直接链接时，他们才可以查看访问包。

## <a name="delete"></a>DELETE

仅当访问包没有活动的用户分配时，才能将其删除。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“访问包”，然后打开访问包。

1. 在左侧菜单中，单击“分配”并删除所有用户的访问权限。

1. 在左侧菜单中单击“概述”，然后单击“删除”。

1. 在出现的删除消息中单击“是”。

## <a name="when-are-changes-applied"></a>何时应用更改

在权利管理中，Azure AD 每天会多次处理访问包中的分配和资源的批量更改。 因此，如果你做了分配或更改了访问包的资源角色，可能需要在长达 24 小时之后，才会在 Azure AD 中做出该项更改，此外，还需要花费一段时间将这些更改传播到其他 Microsoft Online Services 或连接的 SaaS 应用程序。 如果所做的更改只影响几个对象，则更改可能在几分钟后即可在 Azure AD 中应用，之后，其他 Azure AD 组件会检测到该更改并更新 SaaS 应用程序。 如果所做的更改会影响数千个对象，则应用更改需要花费较长的时间。 例如，如果某个访问包具有 2 个应用程序分配和 100 个用户分配，而你决定将一个 SharePoint 站点角色添加到该访问包，则在将所有用户加入 SharePoint 站点角色之前可能会出现延迟。 可以通过 Azure AD 审核日志、Azure AD 预配日志和 SharePoint 站点审核日志来监视进度。

## <a name="next-steps"></a>后续步骤

- [添加目录所有者或访问包管理者](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [请求过程和电子邮件通知](entitlement-management-process.md)
