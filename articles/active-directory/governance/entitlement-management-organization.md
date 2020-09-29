---
title: 在 Azure AD 权利管理中添加连接的组织 - Azure Active Directory
description: 了解如何允许组织外部的人员请求访问包，以便你可以进行项目协作。
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
ms.date: 09/28/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96106cc1d9f9040f98c7d9201f05b4cff87af7e5
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449796"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中添加连接的组织

使用 Azure Active Directory (Azure AD) 权利管理，你可以与组织外部的人员进行协作。 如果你经常与外部 Azure AD 目录或域中的用户协作，则可以将其添加为连接的组织。 本文介绍了如何添加连接的组织，以便允许组织外部的用户请求目录中的资源。

## <a name="what-is-a-connected-organization"></a>什么是连接的组织？

连接的组织是与你有关系的外部 Azure AD 目录或域。

例如，假设你在 Woodgrove Bank 工作，想要与两个外部组织协作。 这两个组织具有不同的配置：

- Graphic Design Institute 使用 Azure AD，其用户的用户主体名称以 graphicdesigninstitute.com 结尾。
- Contoso 尚未使用 Azure AD。 Contoso 用户的用户主体名称以 *contoso.com* 结尾。

在这种情况下，你可以配置两个连接的组织。 分别为 Graphic Design Institute 和 Contoso 创建一个连接的组织。 如果随后将两个连接的组织添加到策略，则每个组织中具有与该策略相匹配的用户主体名称的用户都可以请求访问包。 用户主体名称中的域为 *graphicdesigninstitute.com* 的用户将与 Graphic Design Institute 连接组织匹配，这些用户获允提交请求。 用户主体名称中的域为 *contoso.com* 的用户将与 Contoso 连接组织匹配，这些用户获允请求包。 而且，由于 Graphic Design Institute 使用 Azure AD，因此其主体名称与已添加到其租户的[已验证域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)匹配的任何用户（例如 graphicdesigninstitute.example）也可以通过使用相同的策略来请求访问包。

![连接的组织示例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目录或域中的用户进行身份验证的方式取决于身份验证类型。 连接的组织的身份验证类型为：

- Azure AD
- [直接联合身份验证](../external-identities/direct-federation.md)
- [一次性密码](../external-identities/one-time-passcode.md) (域) 

有关如何添加连接的组织的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>添加连接的组织

若要将外部 Azure AD 目录或域添加为连接的组织，请按照此部分中的说明进行操作。

**必备角色**：全局管理员或用户管理员 

1. 在 Azure 门户中，依次选择“Azure Active Directory”、“标识监管”。 

1. 在左窗格中，选择“连接的组织”，然后选择“添加连接的组织”。

    ![“添加连接的组织”按钮](./media/entitlement-management-organization/connected-organization.png)

1. 选择“基本信息”选项卡，然后输入组织的显示名称和描述。

    ![“添加连接的组织”基本信息窗格](./media/entitlement-management-organization/organization-basics.png)

1. 当你创建新的连接的组织时，状态将自动设置为 " **已配置** "。 有关状态属性的详细信息，请参阅 [已连接组织的状态属性](#state-properties-of-connected-organizations)

1. 选择“目录 + 域”选项卡，然后选择“添加目录 + 域”。

    此时会打开“选择目录 + 域”窗格。

1. 在搜索框中，输入一个域名来搜索 Azure AD 目录或域。 请务必输入整个域名。

1. 验证组织名称和身份验证类型是否正确无误。 用户如何登录取决于身份验证类型。

    ![“选择目录 + 域”窗格](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 选择“添加”以添加 Azure AD 目录或域。 目前只能为每个连接的组织添加一个 Azure AD 目录或域。

    > [!NOTE]
    > 该 Azure AD 目录或域中的所有用户都可请求此访问包。 这包括来自与该目录关联的所有子域的 Azure AD 中的用户，除非这些域被 Azure AD 企业对企业 (B2B) 允许或拒绝列表阻止。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../external-identities/allow-deny-list.md)。

1. 添加 Azure AD 目录或域后，选择“选择”。

    组织将显示在列表中。

    ![“目录 + 域”窗格](./media/entitlement-management-organization/organization-directory-domain.png)

1. 选择“发起人”选项卡，然后为此连接的组织添加可选的发起人。

    发起人是已在你的目录中的内部或外部用户，他们是与此连接的组织建立关系的联系点。 内部发起人是你的目录中的成员用户。 外部发起人是来自连接的组织且以前已受邀并已在你的目录中的来宾用户。 当此连接的组织中的用户请求访问此访问包时，可将发起人用作审批者。 若要了解如何将来宾用户邀请到目录，请参阅[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../external-identities/add-users-administrator.md)。

    选择“添加/删除”时，会打开一个窗格，可在其中选择内部或外部发起人。 此窗格显示你的目录中用户和组的未筛选列表。

    ![“发起人”窗格](./media/entitlement-management-organization/organization-sponsors.png)

1. 选择“查看 + 创建”选项卡，查看你的组织设置，然后选择“创建”。 

    ![“查看 + 创建”窗格](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新连接的组织 

如果连接的组织更改为其他域、组织名称发生更改，或者你想要更改发起人，则可以按照本部分中的说明更新连接的组织。

**必备角色**：全局管理员或用户管理员 

1. 在 Azure 门户中，依次选择“Azure Active Directory”、“标识监管”。 

1. 在左窗格中，选择“连接的组织”，然后选择连接的组织以将其打开。

1. 在连接的组织的 "概述" 窗格中，选择 " **编辑** " 更改组织名称、说明或状态。  

1. 在“目录 + 域”窗格中选择“更新目录 + 域”，改为使用其他目录或域。

1. 在“发起人”窗格中选择“添加内部发起人”或“添加外部发起人”，将某个用户添加为发起人。 若要删除某个发起人，请选择该发起人，然后在右窗格中选择“删除”。


## <a name="delete-a-connected-organization"></a>删除连接的组织

如果你与某个外部 Azure AD 目录或域不再有关系，则可以删除该连接的组织。

**必备角色**：全局管理员或用户管理员 

1. 在 Azure 门户中，依次选择“Azure Active Directory”、“标识监管”。 

1. 在左窗格中，选择“连接的组织”，然后选择连接的组织以将其打开。

1. 在连接的组织的概览窗格中，选择“删除”以将其删除。

    目前，只有不存在连接的用户时，才能删除连接的组织。

    ![连接的组织的“删除”按钮](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>以编程方式管理连接的组织

你还可以使用 Microsoft Graph 创建、列出、更新和删除连接的组织。 通过具有委托的 `EntitlementManagement.ReadWrite.All` 权限的应用程序，相应角色中的用户可以调用 API 来管理 [connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta) 对象并为其设置发起人。

## <a name="state-properties-of-connected-organizations"></a>已连接组织的状态属性

目前，已配置并提议 Azure AD 授权管理中的连接组织有两种不同类型的状态属性： 

- 已配置的已连接组织是一个功能齐全的连接组织，它允许该组织中的用户访问包。 当管理员在 Azure 门户中创建新的连接的组织时，默认情况下，它将处于 " **已配置** " 状态，因为管理员创建并想要使用此连接的组织。 此外，在通过 API 以编程方式创建连接的组织时，应 **配置** 默认状态，除非显式设置为另一个状态。 

    已配置的已连接组织将显示在已连接组织的选取器中，并将在面向 "所有" 连接组织的任何策略的范围内。

- 建议连接的组织是已自动创建的连接的组织，但没有管理员创建或审批组织。 当用户在已配置的连接的组织之外注册访问包时，任何自动创建的已连接组织都将处于 "已 **建议** " 状态，因为租户中没有设置该合作关系的管理员。 
    
    建议连接的组织不会显示在配置连接的组织的选取器中，也不会显示在任何策略的 "所有已配置的已连接组织" 设置的作用域中。 

只有已配置连接的组织中的用户可以请求访问包，这些包可供所有已配置组织的用户使用。 建议连接的组织中的用户可以体验到，因为该域没有连接的组织，并且在管理员更改状态之前，不能访问访问包。

> [!NOTE]
> 在推出这一新功能的过程中，所有在09/09/20 之前创建的已连接组织都被视为 **已配置**。 如果你有允许组织中的用户进行注册的访问包，则应该查看在该日期之前创建的已连接组织的列表，以确保未按 **配置**进行 miscategorized。  管理员可以根据需要更新 **状态** 属性。 有关指南，请参阅 [更新连接的组织](#update-a-connected-organization)。

## <a name="next-steps"></a>后续步骤

- [管控外部用户的访问权限](./entitlement-management-external-users.md)
- [管控不在目录中的用户的访问权限](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
