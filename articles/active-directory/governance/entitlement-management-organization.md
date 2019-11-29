---
title: 在 Azure AD 的权利管理中添加连接的组织-Azure Active Directory
description: 了解如何允许组织外部人员请求访问包，以便你可以对项目进行协作。
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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dec950f5475a8a64cfecfac1fb25246d6a7aa29
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561919"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 的权利管理中添加连接的组织

Azure AD 的权利管理使你可以与组织外的人员进行协作。 如果经常与外部 Azure AD 目录或域中的用户协作，则可以将其添加为连接的组织。 本文介绍如何添加连接的组织，以便允许组织外部的用户请求目录中的资源。

## <a name="what-is-a-connected-organization"></a>什么是连接的组织？

连接的组织是您与之有关系的外部 Azure AD 目录或域。

例如，假设你在 Woodgrove Bank 中工作，并且想要与两个外部组织协作：图形设计研究所和 Contoso。 你已在图形设计研究所向你的联系人通知了 Azure AD，并且该图形设计研究所的用户的用户主体名称以 `graphicdesigninstitute.com`结尾。 你的联系人已在 Contoso 那里通知他们尚未使用 Azure AD，但 Contoso 用户的用户主体名称以 `contoso.com`结尾。

你可以配置两个已连接组织--一个用于具有域 `graphicdesigninstitute.com`的图形设计研究所，另一个用于 Contoso 域 `contoso.com`。 如果然后将这两个已连接组织添加到策略中，则每个组织中具有与该策略相匹配的用户主体名称的用户都可以请求访问包。 此外，由于图形设计研究所已被标识为使用 Azure AD，因此，如果图形设计研究所随后具有子域（如 `graphicdesigninstitute.example`），则具有该用户主体名称的用户也可以使用相同的策略请求访问包。

![连接的组织示例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目录或域中的用户进行身份验证的方式取决于身份验证类型。 连接的组织的身份验证类型如下：

- Azure AD
- [直接联合](../b2b/direct-federation.md)
- [一次性密码](../b2b/one-time-passcode.md)（域）

有关如何添加连接的组织的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>添加连接的组织

按照以下步骤添加外部 Azure AD 目录或域作为已连接的组织。

**必备角色：** 全局管理员、用户管理员或来宾邀请者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**连接组织**"，然后单击 "**添加连接的组织**"。

    ![身份管理连接的组织-添加连接的组织](./media/entitlement-management-organization/connected-organization.png)

1. 在 "**基本**信息" 选项卡上，输入组织的显示名称和描述。

    ![添加连接的组织-基本信息选项卡](./media/entitlement-management-organization/organization-basics.png)

1. 在 "**目录 + 域**" 选项卡上，单击 "**添加目录 + 域**"，打开 "选择目录 + 域" 窗格。

1. 键入要搜索 Azure AD 目录或域的域名。 必须键入整个域名。

1. 按照提供的名称和身份验证类型验证它是否是正确的组织。 用户登录的方式取决于身份验证类型。

    ![添加连接的组织-选择目录 + 域](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 单击 "**添加**" 以添加 Azure AD 的目录或域。 目前只能为每个连接的组织添加一个 Azure AD 目录或域。

    > [!NOTE]
    > Azure AD 目录或域中的所有用户都将能够请求此访问包。 这包括从与目录关联的所有子域 Azure AD 中的用户，除非这些域被 Azure B2B 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../b2b/allow-deny-list.md)。

1. 添加 Azure AD 目录或域后，单击 "**选择**"。

    组织将显示在列表中。

    ![添加连接的组织-目录选项卡](./media/entitlement-management-organization/organization-directory-domain.png)

1. 在 "**发起方**" 选项卡上，添加此连接的组织的可选赞助商。

    主办方是已在你的目录中的内部或外部用户，即与此连接的组织建立关系的联系点。 内部主办方是你目录中的成员用户。 外部主办方是来自已连接组织、以前受邀并已在你的目录中的来宾用户。 当此连接的组织中的用户请求访问此访问包时，可将主办方用作审批者。 有关如何将来宾用户邀请到目录的信息，请参阅[在 Azure 门户中添加 AZURE ACTIVE DIRECTORY B2B 协作用户](../b2b/add-users-administrator.md)。

    单击 "**添加/删除**" 时，将显示一个窗格，以选择内部或外部主办方。 此窗格显示目录中用户和组的未筛选列表。

    ![访问包-策略-添加已连接组织-赞助者选项卡](./media/entitlement-management-organization/organization-sponsors.png)

1. 在 "**查看**" 和 "创建" 选项卡上，查看你的组织设置，然后单击 "**创建**"。

    ![访问包-策略-添加已连接组织-审阅 + 创建选项卡](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>删除连接的组织

如果不再与外部 Azure AD 目录或域建立关系，可以删除连接的组织。

**必备角色：** 全局管理员、用户管理员或来宾邀请者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**连接组织**"，然后单击打开连接的组织。

1. 在 "概述" 页上，单击 "**删除**" 以删除连接的组织。

    目前，如果没有连接的用户，则只能删除连接的组织。

    ![身份管理连接的组织-删除连接的组织](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>后续步骤

- [为外部用户管理访问权限](entitlement-management-organization.md)
- [对于不在你的目录中的用户](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
