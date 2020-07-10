---
title: 在 Azure AD 的权利管理中添加连接的组织-Azure Active Directory
description: 了解如何允许组织外部人员请求访问包，以便你可以对项目进行协作。
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
ms.openlocfilehash: 272dd95b97c65ecc52dd73909f1ed87d5e5ae3ca
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170490"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 的权利管理中添加连接的组织

利用 Azure Active Directory (Azure AD) 的权限管理，你可以与组织外的人员进行协作。 如果经常与外部 Azure AD 目录或域中的用户协作，则可以将其添加为连接的组织。 本文介绍如何添加连接的组织，以便允许组织外部的用户请求目录中的资源。

## <a name="what-is-a-connected-organization"></a>什么是连接的组织？

连接的组织是您与之有关系的外部 Azure AD 目录或域。

例如，假设您在 Woodgrove Bank 中工作，并且您想要与两个外部组织协作。 这两个组织具有不同的配置：

- 图形设计研究所使用 Azure AD，其用户的用户主体名称以*graphicdesigninstitute.com*结尾。
- Contoso 尚未使用 Azure AD。 Contoso 用户的用户主体名称以*contoso.com*结尾。

在这种情况下，你可以配置两个连接的组织。 为图形设计研究所创建一个连接的组织，为 Contoso 创建一个。 如果随后将两个已连接的组织添加到策略，则每个组织中具有与该策略相匹配的用户主体名称的用户都可以请求访问包。 用户主体名称具有*graphicdesigninstitute.com*域的用户将匹配图形设计研究所连接的组织，并允许提交请求。 用户主体名称具有*contoso.com*域的用户将匹配 contoso 连接的组织，并且还允许请求包。 而且，由于图形设计研究所使用 Azure AD，其主体名称与添加到其租户的[已验证域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)匹配的任何用户（例如*graphicdesigninstitute*）也可以通过使用相同的策略请求访问包。

![连接的组织示例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目录或域中的用户进行身份验证的方式取决于身份验证类型。 连接的组织的身份验证类型为：

- Azure AD
- [直接联合身份验证](../b2b/direct-federation.md)
- [一次性密码](../b2b/one-time-passcode.md) (域) 

有关如何添加连接的组织的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>添加连接的组织

若要将外部 Azure AD 目录或域添加为连接的组织，请按照本部分中的说明进行操作。

**必备角色**：*全局管理员*或*用户管理员*

1. 在 Azure 门户中，选择 " **Azure Active Directory**"，然后选择 "**标识调控**"。

1. 在左窗格中，选择 "**已连接组织**"，然后选择 "**添加连接的组织**"。

    !["添加连接的组织" 按钮](./media/entitlement-management-organization/connected-organization.png)

1. 选择 "**基本**信息" 选项卡，然后输入组织的显示名称和描述。

    !["添加连接的组织" 基本信息窗格](./media/entitlement-management-organization/organization-basics.png)

1. 选择 "**目录 + 域**" 选项卡，然后选择 "**添加目录**" 和 "域"。

    此时将打开 "**选择目录 + 域**" 窗格。

1. 在 "搜索" 框中，输入要搜索 Azure AD 目录或域的域名。 请务必输入整个域名。

1. 验证组织名称和身份验证类型正确无误。 用户登录的方式取决于身份验证类型。

    !["选择目录 + 域" 窗格](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 选择 "**添加**" 以添加 Azure AD 目录或域。 目前只能为每个连接的组织添加一个 Azure AD 目录或域。

    > [!NOTE]
    > Azure AD 目录或域中的所有用户都将能够请求此访问包。 这包括从与目录关联的所有子域 Azure AD 中的用户，除非这些域被 Azure AD business to business (B2B) 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../b2b/allow-deny-list.md)。

1. 添加 Azure AD 目录或域后，选择 "**选择**"。

    组织将显示在列表中。

    !["目录 + 域" 窗格](./media/entitlement-management-organization/organization-directory-domain.png)

1. 选择 "**主办方**" 选项卡，然后为此连接的组织添加可选的主办方。

    主办方是已在你的目录中的内部或外部用户，即与此连接的组织建立关系的联系点。 内部主办方是你目录中的成员用户。 外部主办方是来自已连接组织、以前受邀并已在你的目录中的来宾用户。 当此连接的组织中的用户请求访问此访问包时，可将主办方用作审批者。 有关如何将来宾用户邀请到目录的信息，请参阅[在 Azure 门户中添加 AZURE ACTIVE DIRECTORY B2B 协作用户](../b2b/add-users-administrator.md)。

    选择 "**添加/删除**" 时，将打开一个窗格，可在其中选择内部或外部主办方。 此窗格显示目录中用户和组的未筛选列表。

    ![赞助方窗格](./media/entitlement-management-organization/organization-sponsors.png)

1. 选择 "**查看**" 和 "创建" 选项卡，查看你的组织设置，然后选择 "**创建**"。

    !["查看和创建" 窗格](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新连接的组织 

如果连接的组织更改为其他域、组织名称发生更改，或者您想要更改主办方，则可以按照本部分中的说明更新连接的组织。

**必备角色**：*全局管理员*、*用户管理员*或*来宾邀请者*

1. 在 Azure 门户中，选择 " **Azure Active Directory**"，然后选择 "**标识调控**"。

1. 在左窗格中，选择 "**已连接组织**"，然后选择连接的组织将其打开。

1. 在连接的组织的 "概述" 窗格中，选择 "**编辑**" 更改组织名称或描述。  

1. 在 "**目录 + 域**" 窗格中，选择 "**更新目录 + 域**" 以更改为其他目录或域。

1. 在 "**发起方**" 窗格中，选择 "**添加内部主办方**" 或 "**添加外部赞助**商"，将用户添加为主办方。 若要删除主办方，请选择主办方，并在右窗格中选择 "**删除**"。


## <a name="delete-a-connected-organization"></a>删除连接的组织

如果不再与外部 Azure AD 目录或域建立关系，可以删除连接的组织。

**必备角色**：*全局管理员*、*用户管理员*或*来宾邀请者*

1. 在 Azure 门户中，选择 " **Azure Active Directory**"，然后选择 "**标识调控**"。

1. 在左窗格中，选择 "**已连接组织**"，然后选择连接的组织将其打开。

1. 在连接的组织的 "概述" 窗格中，选择 "**删除**" 将其删除。

    目前，只有在没有连接的用户的情况下，才能删除连接的组织。

    ![连接的组织 "删除" 按钮](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>后续步骤

- [管控外部用户的访问权限](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [控制不在你的目录中的用户的访问权限](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
