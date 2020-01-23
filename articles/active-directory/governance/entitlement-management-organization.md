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
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1b6f5ebffa39d3b735e85df794e37329e3aa2e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548894"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 的权利管理中添加连接的组织

Azure AD 的权利管理使你可以与组织外的人员进行协作。 如果经常与外部 Azure AD 目录或域中的用户协作，则可以将其添加为连接的组织。 本文介绍如何添加连接的组织，以便允许组织外部的用户请求目录中的资源。

## <a name="what-is-a-connected-organization"></a>什么是连接的组织？

连接的组织是您与之有关系的外部 Azure AD 目录或域。

例如，假设您在 Woodgrove Bank 中工作，并且您想要与两个外部组织协作。 这两个组织具有不同的配置：

- 图形设计研究所使用 Azure AD，其用户的用户主体名称以 `graphicdesigninstitute.com`
- Contoso 尚未使用 Azure AD。 Contoso 用户的用户主体名称以 `contoso.com`结尾。

在这种情况下，你可以配置两个连接的组织。 您将为图形设计研究所创建一个连接的组织，为 Contoso 创建一个。 如果然后将这两个已连接组织添加到策略中，则每个组织中用户主体名称与策略匹配的用户都可以请求访问包。 用户主体名称具有 graphicdesigninstitute.com 域的用户将与图形设计研究所连接的组织匹配，并允许提交请求，而用户主体名称具有 contoso.com 域的用户将匹配Contoso 连接的组织和还允许请求包。 此外，因为图形设计研究所使用 Azure AD，因此，主体名称与[已验证的域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)（如 graphicdesigninstitute）相匹配的任何用户也将能够使用相同的策略请求访问包。

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

- [为外部用户管理访问权限](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [对于不在你的目录中的用户](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
