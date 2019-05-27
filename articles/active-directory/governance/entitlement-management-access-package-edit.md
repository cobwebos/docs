---
title: 编辑和管理 Azure AD 授权管理 （预览版）-Azure Active Directory 中的现有访问包
description: 了解如何编辑和管理 Azure Active Directory 权利管理 （预览版） 中的现有访问包。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833373"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>编辑和管理 Azure AD 授权管理 （预览版） 中的现有访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 授权管理当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

访问包便可以执行一次性的安装程序的资源和策略自动管理生命周期内访问包的访问权限。 作为访问包管理器中，可以在任何时候更改访问包中的资源，而无需担心预配用户的访问新的资源，或从以前的资源中删除其访问权限。 此外可以在任何时候更新策略，但是，策略更改仅影响新的访问。

本文介绍如何编辑和管理现有访问包。

## <a name="add-resource-roles"></a>添加资源角色

在资源角色是与资源关联的权限集合。 使资源可供用户请求的方法是通过将资源角色添加到你访问包。 你可以添加资源组、 应用程序以及 SharePoint 站点的角色。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 在左侧菜单中，单击**资源角色**。

1. 单击**添加资源角色**以打开添加资源角色以访问包页。

    ![包具有访问权限-添加资源角色](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 具体取决于是否想要添加组、 应用程序或 SharePoint 站点，执行以下资源角色部分中的步骤。

### <a name="add-a-group-resource-role"></a>添加组资源角色

您可以在分配访问包时会自动将用户添加到组的权利管理。 

- 当一组访问包和用户的一部分被分配给该访问包，用户添加到该组中，如果尚不存在。
- 当用户的访问权限包分配到期时，它们从组中删除，除非它们当前具有到另一个访问包，其中包含该相同组分配。

可以选择任何 Office 365 组或 Azure AD 安全组。  管理员可以将任何组添加到目录;目录所有者可以将任何组添加到目录，如果它们是组的所有者。 选择一个组时，请记住以下 Azure AD 限制：

- 当包括来宾用户作为成员添加到组时，他们可以查看该组的所有其他成员。
- 从 Windows Server Active Directory 使用 Azure AD Connect，azure AD 不能更改已同步的组成员身份。  
- 不能通过添加或删除成员，因此不适合与授权管理一起使用动态组成员身份更新动态组的成员身份。

1. 上**添加包具有访问权限的资源角色**页上，单击**组**以打开选择组窗格。

1. 选择你想要访问包中包含的组。

    ![包具有访问权限-添加资源角色-选择的组](./media/entitlement-management-access-package-edit/group-select.png)

1. 单击“选择”。

1. 在中**角色**列表中，选择**所有者**或**成员**。

    你通常选择成员角色。 如果选择所有者角色，以便用户能添加或删除其他成员或所有者。

    ![包具有访问权限-添加的组的资源角色](./media/entitlement-management-access-package-edit/group-role.png)

1. 单击“添加”。

    添加时，具有访问包的现有分配的任何用户将自动成为此组的成员。

### <a name="add-an-application-resource-role"></a>添加应用程序资源角色

您可以自动将用户分配访问到 Azure AD 企业应用程序，包括 SaaS 应用程序和联合到 Azure AD 中，为用户分配访问包的组织的应用程序的 Azure AD。 对于应用程序与 Azure AD 通过联合单一登录，Azure AD 将颁发用户分配到应用程序的联合身份验证的令牌。

应用程序可以具有多个角色。 如果该应用程序具有多个角色中添加到访问包中，应用程序，需要指定相应的角色为这些用户。  如果正在开发的应用程序，您可以阅读更多有关这些角色提供给对如何的文章中的应用程序[配置 SAML 令牌中颁发角色声明](../develop/active-directory-enterprise-app-role-management.md)。

一旦应用程序角色是访问包的一部分：

- 如果将用户分配该访问包，该用户添加到该应用程序角色，如果尚不存在。
- 当用户的访问权限包分配到期时，他们的访问权限将删除从应用程序，除非它们具有到其他访问包中包含该应用程序角色分配。

选择应用程序时，下面是一些注意事项：

- 应用程序还可能具有分配给其角色组。  您可以选择在访问包中，但是添加代替应用程序角色的组，然后在应用程序不会对用户可见我访问门户中访问包的一部分。

1. 上**添加包具有访问权限的资源角色**页上，单击**应用程序**以打开选择应用程序窗格。

1. 选择你想要访问包中包含的应用程序。

    ![包具有访问权限-添加资源角色-选择应用程序](./media/entitlement-management-access-package-edit/application-select.png)

1. 单击“选择”。

1. 在中**角色**列表中，选择应用程序角色。

    ![包具有访问权限-添加应用程序的资源角色](./media/entitlement-management-access-package-edit/application-role.png)

1. 单击“添加”。

    具有访问包的现有分配的任何用户将自动被赋予访问此应用程序时将其添加。

### <a name="add-a-sharepoint-site-resource-role"></a>添加 SharePoint 站点资源角色

Azure AD 可以自动将用户分配访问到 SharePoint Online 网站或 SharePoint Online 网站集时，会为它们分配访问包。

1. 上**添加包具有访问权限的资源角色**页上，单击**SharePoint 站点**以打开选择 SharePoint Online 站点窗格中。

1. 选择你想要包括在访问包中的 SharePoint Online 站点。

    ![包具有访问权限-添加资源角色-选择 SharePoint Online 网站](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. 单击“选择”。

1. 在中**角色**列表中，选择 SharePoint Online 站点角色。

    ![包具有访问权限-添加的 SharePoint Online 网站的资源角色](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. 单击“添加”。

    具有访问包的现有分配的任何用户将自动被赋予访问此 SharePoint Online 站点时将其添加。

## <a name="remove-resource-roles"></a>删除资源角色

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 在左侧菜单中，单击**资源角色**。

1. 在资源角色的列表中，找到你想要删除的资源角色。

1. 单击省略号 (**...**)，然后单击**删除资源角色**。

    具有访问包的现有分配的任何用户将自动具有它不再向此资源角色撤消其访问权限。

## <a name="add-a-new-policy"></a>添加新策略

您指定谁可以请求访问包的方式是创建一个策略。 如果你想要允许不同的用户分配使用不同的审批和过期设置要向其授予集，可以创建为单一访问包的多个策略。 单个策略不能用于将内部和外部用户分配到同一个访问包。 但是，可以创建两个策略中的相同访问包-一个用于内部用户，另一个用于外部用户。 如果有多个应用于用户的策略，将在其请求选择他们想要分配给时提示他们。

下图显示了创建适用于现有访问包的策略的高级过程。

![创建策略过程](./media/entitlement-management-access-package-edit/policy-process.png)

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 单击**策略**，然后**添加策略**。

1. 键入的名称和策略的描述。

    ![创建策略具有名称和说明](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. 基于为所选**可以请求访问用户**，执行下列策略部分之一中的步骤。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>编辑现有策略

可以随时编辑策略。 如果更改策略的到期日期，不会更改已在等待批准或已批准状态请求的过期日期。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 单击**策略**，然后单击你想要编辑的策略。

    **策略的详细信息**窗格将在页面底部打开。

    ![访问包的策略的详细信息窗格](./media/entitlement-management-access-package-edit/policy-details.png)

1. 单击**编辑**来编辑策略。

    ![访问包的编辑策略](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 完成后，单击**更新**。

## <a name="directly-assign-a-user"></a>将用户直接分配

在某些情况下，你可能想要直接将特定用户分配给访问包，以便用户无需经历的请求访问包过程。 若要直接将用户分配，访问包必须具有允许管理员直接分配的策略。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 在左侧菜单中，单击**分配**。

1. 单击**新的分配**以打开用户添加到访问包。

    ![分配-添加到访问包的用户](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. 单击**将用户添加**选择你想要分配到访问包的用户。

1. 在中**选择策略**列表中，选择一个策略，具有[None （仅管理员直接分配）](#policy-none-administrator-direct-assignments-only)设置。

    如果此访问包不具有此类型的策略，则可以单击**创建新的策略**添加一个。

1. 设置所选的用户的分配，以开始和结束的日期和时间。 如果未提供的结束日期，将使用该策略的过期时间设置。

1. （可选） 提供用于保存记录你直接分配的理由。

1. 单击**添加**若要将所选的用户直接分配到访问包。

    几分钟后，单击**刷新**查看在分配列表中的用户。

## <a name="view-who-has-an-assignment"></a>查看具有赋值

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 单击**分配**若要查看的活动分配列表。

1. 单击一个特定的分配，以查看其他详细信息。

1. 若要查看没有正确预配的所有资源角色分配列表，请单击筛选器状态，然后选择**交付**。

    可以在传递错误查看其他详细信息，通过在上找到用户的相应请求**请求**页。

1. 若要查看已过期的分配，请单击筛选器状态，然后选择**已过期**。

1. 若要下载的筛选列表的 CSV 文件，请单击**下载**。

## <a name="view-requests"></a>查看请求

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 单击**请求**。

1. 单击特定的请求，以查看其他详细信息。

## <a name="view-a-requests-delivery-errors"></a>查看请求的传递错误

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 单击**请求**。

1. 选择你想要查看的请求。

    如果请求具有任何传递错误，将为请求的状态**Undelivered**和子状态将为**部分传递**。

    如果有任何传递的错误，请求的详细信息窗格中，将传递错误的计数。

1. 单击要查看所有请求的传递错误的计数。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

只可以取消尚未传递的挂起请求。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 单击**请求**。

1. 单击你想要取消的请求

1. 在请求的详细信息窗格中，单击**取消请求**。

## <a name="copy-my-access-portal-link"></a>复制我访问门户链接

你的目录中的大多数用户可以登录到我访问门户，并会自动看到他们可以请求访问包的列表。 但是，对于尚不在你目录中的外部业务合作伙伴用户，你将需要向他们发送一个链接，它们可用于请求访问包。 只要为外部用户启用访问包，并且必须为外部用户的目录的策略，外部用户可以使用我访问门户链接来请求访问包。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 在概述页上，复制**我访问门户链接**。

    ![访问包概述-我访问门户链接](./media/entitlement-management-shared/my-access-portal-link.png)

1. 电子邮件或将链接发送给您的外部业务合作伙伴。 它们可以与他们的用户请求访问包共享的链接。

## <a name="change-the-hidden-setting"></a>更改隐藏设置

访问包都是默认情况下可发现。 这意味着，如果策略允许用户请求访问包，它们会自动将看到列出其我访问门户中访问包。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 在概述页上单击**编辑**。

1. 设置**Hidden**设置。

    如果设置为**否**，将用户的我访问门户中列出访问包。

    如果设置为**是**，访问包不会在用户的我访问门户中列出。 用户可以查看访问包的唯一方法是如果它们具有直接**我访问门户链接**到访问包。

## <a name="delete"></a>删除

如果不有任何活动的用户分配，仅可以删除访问包。

**必备角色：** 用户管理员、 目录所有者或访问包管理器

1. 在 Azure 门户中，单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**再打开访问包。

1. 在左侧菜单中，单击**分配**和删除所有用户的访问权限。

1. 在左侧菜单中，单击**概述**，然后单击**删除**。

1. 在删除出现的消息中，单击**是**。

## <a name="when-are-changes-applied"></a>当应用更改

在授权管理 Azure AD 将处理分配和访问包中的资源的大容量更改每天多次。 因此，如果进行分配，或更改的资源角色的访问包时，可能需要 24 小时在 Azure AD 中进行该更改，加上的时间它需要将这些更改传播到其他 Microsoft Online Services 或连接的 SaaS 应用程序s。 如果所做的更改会影响只需几个对象，更改可能只需要几分钟来应用在 Azure AD 中之后, 其他 Azure AD 组件将然后检测的更改并更新 SaaS 应用程序。 如果所做的更改会影响数千个对象，更改将会更长。 例如，如果你具有访问包使用 2 个应用程序和 100 的用户分配，并且你决定将 SharePoint 站点角色添加到访问包，可能有延迟直到所有用户都都属于该 SharePoint 站点角色。 你可以监视通过 Azure AD 审核日志、 Azure AD 预配日志，以及 SharePoint 站点审核日志的进度。

## <a name="next-steps"></a>后续步骤

- [请求过程和电子邮件通知](entitlement-management-process.md)
