---
title: 在 Azure AD 授权管理 (预览版) 中编辑和管理现有访问包-Azure Active Directory
description: 了解如何在 Azure Active Directory 授权管理 (预览版) 中编辑和管理现有访问包。
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c85d53d05193313f9e166b88beb2a0f82eb197
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618373"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 授权管理 (预览版) 中编辑和管理现有访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用访问包可以对访问包的生命周期自动管理访问权限的资源和策略进行一次性设置。 作为访问包管理器, 你可以随时更改访问包中的资源, 而无需担心设置用户对新资源的访问权限, 或者从以前的资源中删除其访问权限。 不过, 策略也可以随时更新, 但策略更改只会影响新的访问。

本文介绍如何编辑和管理现有访问包。

## <a name="add-resource-roles"></a>添加资源角色

资源角色是与资源关联的权限的集合。 向用户提供资源以供用户请求的方式是将资源角色添加到访问包。 可以添加组、应用程序和 SharePoint 站点的资源角色。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 在左侧菜单中, 单击 "**资源角色**"。

1. 单击 "**添加资源角色**" 以打开 "将资源角色添加到 access 包" 页面。

    ![访问包-添加资源角色](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 根据你是要添加组、应用程序还是 SharePoint 站点, 请执行下列资源角色部分之一中的步骤。

### <a name="add-a-group-resource-role"></a>添加组资源角色

当向用户分配访问包时, 可以让权利管理自动将用户添加到组。 

- 当某个组是访问包的一部分并且将用户分配到该访问包时, 该用户将添加到该组 (如果尚未存在)。
- 当用户的访问包分配过期时, 它们将从组中删除, 除非它们当前已分配到包含同一组的其他访问包。

你可以选择任何 Office 365 组或 Azure AD 安全组。  管理员可以将任何组添加到目录;如果目录所有者是组的所有者, 则可以将任何组添加到目录中。 选择组时, 请牢记以下 Azure AD 约束:

- 如果用户 (包括来宾) 作为成员添加到组中, 则他们可以看到该组的所有其他成员。
- Azure AD 无法使用 Azure AD Connect 更改从 Windows Server Active Directory 同步的组的成员身份, 或者无法更改在 Exchange Online 中创建为通讯组的组的成员身份。  
- 动态组的成员身份无法通过添加或删除成员进行更新, 因此动态组成员身份不适用于授权管理。

1. 在 "**将资源角色添加到 access 包**" 页面上, 单击 "**组**" 以打开 "选择组" 窗格。

1. 选择要包括在访问包中的组。

    ![访问包-添加资源角色-选择组](./media/entitlement-management-access-package-edit/group-select.png)

1. 单击“选择”。

1. 在 "**角色**" 列表中, 选择 "**所有者**" 或 "**成员**"。

    通常选择成员角色。 如果选择 "所有者" 角色, 则允许用户添加或删除其他成员或所有者。

    ![访问包-添加组的资源角色](./media/entitlement-management-access-package-edit/group-role.png)

1. 单击“添加”。

    添加到访问包的任何用户将在添加此组时自动成为该组的成员。

### <a name="add-an-application-resource-role"></a>添加应用程序资源角色

可以 Azure AD 让用户在分配访问包时, 自动为用户分配对 Azure AD 企业应用程序的访问权限, 包括 SaaS 应用程序和联合到 Azure AD 的组织的应用程序。 对于通过联合单一登录与 Azure AD 相集成的应用程序, Azure AD 将为分配给该应用程序的用户颁发联合身份验证令牌。

应用程序可以有多个角色。 将应用程序添加到访问包时, 如果该应用程序有多个角色, 则需要为这些用户指定适当的角色。  如果要开发应用程序, 可以在如何[配置 SAML 令牌中颁发的角色声明](../develop/active-directory-enterprise-app-role-management.md)一文中阅读有关如何向应用程序提供这些角色的详细信息。

一旦应用程序角色是访问包的一部分:

- 向用户分配该访问包时, 会将该用户添加到该应用程序角色 (如果尚未存在)。
- 当用户的访问包分配过期时, 将从应用程序中删除其访问权限, 除非他们分配到另一个包含该应用程序角色的访问包。

选择应用程序时, 需要注意以下事项:

- 应用程序还可以将组分配给其角色。  你可以选择在访问包中添加组来代替应用程序角色, 但是, 应用程序在 "我的访问门户" 中作为访问包的一部分对用户不可见。

1. 在 "**将资源角色添加到 access 包**" 页面上, 单击 "**应用程序**" 以打开 "选择应用程序" 窗格。

1. 选择要包括在访问包中的应用程序。

    ![访问包-添加资源角色-选择应用程序](./media/entitlement-management-access-package-edit/application-select.png)

1. 单击“选择”。

1. 在 "**角色**" 列表中, 选择应用程序角色。

    ![访问包-添加应用程序的资源角色](./media/entitlement-management-access-package-edit/application-role.png)

1. 单击“添加”。

    如果添加了对访问包的现有分配, 则这些用户将自动获得对此应用程序的访问权限。

### <a name="add-a-sharepoint-site-resource-role"></a>添加 SharePoint 站点资源角色

当向用户分配访问包时, Azure AD 可以自动为用户分配对 SharePoint Online 网站或 SharePoint Online 网站集的访问权限。

1. 在 "**将资源角色添加到 access 包**" 页面上, 单击 " **sharepoint 站点**" 以打开 "选择 sharepoint Online 网站" 窗格。

1. 选择要包含在访问包中的 SharePoint Online 站点。

    ![访问包-添加资源角色-选择 SharePoint Online 网站](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. 单击“选择”。

1. 在 "**角色**" 列表中, 选择 "SharePoint Online 站点" 角色。

    ![访问包-添加 SharePoint Online 网站的资源角色](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. 单击“添加”。

    添加具有对访问包的现有分配的任何用户时, 将自动获得对此 SharePoint Online 站点的访问权限。

## <a name="remove-resource-roles"></a>删除资源角色

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 在左侧菜单中, 单击 "**资源角色**"。

1. 在资源角色列表中, 找到要删除的资源角色。

1. 单击省略号 ( **...** ), 然后单击 "**删除资源角色**"。

    删除此资源角色后, 对该访问包具有现有分配权限的任何用户都将自动撤消其访问权限。

## <a name="add-a-new-policy"></a>添加新策略

指定可请求访问包的用户的方式是创建一个策略。 如果希望允许不同的用户组通过不同的审批和过期设置授予分配, 可以为单个访问包创建多个策略。 单个策略不能用于将内部和外部用户分配到相同的访问包。 但是, 你可以在同一个访问包中创建两个策略, 一个用于内部用户, 另一个用于外部用户。 如果有多个策略应用于用户, 则在请求时, 系统将提示他们选择要分配到的策略。

下图显示了为现有访问包创建策略的高级过程。

![创建策略进程](./media/entitlement-management-access-package-edit/policy-process.png)

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 单击 "**策略**", 然后单击 "**添加策略**"。

1. 键入策略的名称和描述。

    ![创建具有名称和说明的策略](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. 根据你对**可以请求访问权限的用户**的选择, 执行以下策略部分之一中的步骤。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>编辑现有策略

你可以随时编辑策略。 如果更改策略的到期日期, 则已处于 "等待审批" 或 "已批准" 状态的请求的到期日期将不会更改。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 单击 "**策略**", 然后单击要编辑的策略。

    "**策略详细信息**" 窗格将在页面底部打开。

    ![访问包-"策略详细信息" 窗格](./media/entitlement-management-access-package-edit/policy-details.png)

1. 单击 "**编辑**" 以编辑策略。

    ![访问包-编辑策略](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 完成后，单击“更新”。

## <a name="directly-assign-a-user"></a>直接分配用户

在某些情况下, 你可能希望将特定用户直接分配到访问包, 以便用户无需完成请求访问包的过程。 若要直接分配用户, 访问包必须具有允许管理员直接分配的策略。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 在左侧菜单中, 单击 "**分配**"。

1. 单击 "**新建分配**", 打开 "添加用户到 access 包"。

    ![分配-将用户添加到 access 包](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. 单击 "**添加用户**", 选择要为其分配访问包的用户。

1. 在 "**选择策略**" 列表中, 选择具有 "[无 (仅管理员直接分配)](#policy-none-administrator-direct-assignments-only) " 设置的策略。

    如果此访问包没有此类型的策略, 你可以单击 "**创建新策略**" 来添加一个。

1. 设置所选用户要开始和结束的日期和时间。 如果未提供结束日期, 则将使用该策略的过期设置。

1. (可选) 为保留记录的直接分配提供理由。

1. 单击 "**添加**" 以将所选用户直接分配到访问包。

    几分钟后, 单击 "**刷新**" 以查看 "分配" 列表中的用户。

## <a name="view-who-has-an-assignment"></a>查看谁有分配

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 单击 "**分配**" 查看活动分配列表。

1. 单击特定分配查看其他详细信息。

1. 若要查看未正确预配所有资源角色的分配的列表, 请单击筛选器状态, 然后选择 "**传递**"。

    可以通过在**请求**页上查找用户的相应请求来查看有关传递错误的其他详细信息。

1. 若要查看过期的分配, 请单击筛选器状态, 然后选择 "**过期**"。

1. 若要下载筛选列表的 CSV 文件, 请单击 "**下载**"。

## <a name="view-requests"></a>查看请求

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 单击 "**请求**"。

1. 单击特定的请求以查看更多详细信息。

## <a name="view-a-requests-delivery-errors"></a>查看请求的传递错误

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 单击 "**请求**"。

1. 选择要查看的请求。

    如果请求有任何传递错误, 将无法**传递**请求状态, 并且子状态将被**部分传递**。

    如果有任何传递错误, 则会在请求的详细信息窗格中出现传递错误计数。

1. 单击 "计数" 以查看请求的所有传递错误。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

只能取消尚未传递的挂起的请求。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 单击 "**请求**"。

1. 单击要取消的请求

1. 在 "请求详细信息" 窗格中, 单击 "**取消请求**"。

## <a name="copy-my-access-portal-link"></a>复制我的访问门户链接

目录中的大多数用户都可以登录到 "我的访问门户" 并自动查看他们可以请求的访问包的列表。 但是, 对于尚未在你的目录中的外部业务合作伙伴用户, 你将需要向他们发送一个可用于请求访问包的链接。 只要为外部用户启用了访问包, 并且拥有外部用户目录的策略, 外部用户就可以使用 "我的访问门户" 链接来请求访问包。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 在 "概述" 页上, 复制 "**我的 Access 门户" 链接**。

    ![访问包概述 - 我的访问权限门户链接](./media/entitlement-management-shared/my-access-portal-link.png)

1. 发送电子邮件或发送到外部业务合作伙伴的链接。 它们可以与其用户共享链接来请求访问包。

## <a name="change-the-hidden-setting"></a>更改隐藏设置

默认情况下, 访问包是可发现的。 这意味着, 如果策略允许用户请求访问包, 则他们将自动看到其 "我的 Access" 门户中列出的访问包。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 在 "概述" 页上, 单击 "**编辑**"。

1. 设置**隐藏**设置。

    如果设置为 "**否**", 则访问包将在用户的我的访问门户中列出。

    如果设置为 **"是"** , 则访问包将不会在用户的我的访问门户中列出。 如果用户拥有访问包的 "直接**访问" 门户链接**, 则用户可以查看访问包的唯一方式就是。

## <a name="delete"></a>DELETE

仅当访问包没有活动的用户分配时, 才能将其删除。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中, 单击 "**访问包**", 然后打开访问包。

1. 在左侧菜单中, 单击 "**分配**" 并删除所有用户的访问权限。

1. 在左侧菜单中, 单击 "**概述**", 然后单击 "**删除**"。

1. 在出现的 "删除" 消息中, 单击 **"是"** 。

## <a name="when-are-changes-applied"></a>何时应用更改

在 "授权管理" 中, Azure AD 将每天多次处理访问包中的分配和资源的批量更改。 因此, 如果您执行分配或更改访问包的资源角色, 则可能需要长达24小时才能在 Azure AD 中进行该更改, 以及将这些更改传播到其他 Microsoft Online Services 或连接的 SaaS 应用程序所需的时间量。些. 如果你所做的更改只影响几个对象, 则更改可能只需要几分钟的时间才能应用到 Azure AD 中, 之后其他 Azure AD 组件将检测到该更改并更新 SaaS 应用程序。 如果更改会影响数千个对象, 则所做的更改将需要更长的时间。 例如, 如果你有一个具有2个应用程序的访问包和100个用户分配, 并且你决定将 SharePoint 站点角色添加到访问包, 则可能会有延迟, 直到所有用户都是该 SharePoint 站点角色的一部分。 可以通过 Azure AD 审核日志、Azure AD 预配日志和 SharePoint 站点审核日志来监视进度。

## <a name="next-steps"></a>后续步骤

- [添加目录所有者或访问包管理器](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [请求进程和电子邮件通知](entitlement-management-process.md)
