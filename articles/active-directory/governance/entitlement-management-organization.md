---
title: 在 Azure AD 授权管理中添加已连接的组织 - Azure 活动目录
description: 了解如何允许组织外部人员请求访问包，以便可以协作处理项目。
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
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128612"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中添加已连接的组织

使用 Azure 活动目录 （Azure AD） 授权管理，可以与组织外部的人员协作。 如果经常与外部 Azure AD 目录或域中的用户协作，则可以将它们添加为已连接的组织。 本文介绍如何添加已连接的组织，以便允许组织外部的用户请求目录中的资源。

## <a name="what-is-a-connected-organization"></a>什么是互联组织？

已连接的组织是您与其有关系的外部 Azure AD 目录或域。

例如，假设您在 Woodgrove Bank 工作，并且希望与两个外部组织协作。 这两个组织有不同的配置：

- 图形设计研究所使用 Azure AD，其用户具有以*graphicdesigninstitute.com*结尾的用户主体名称。
- Contoso 尚未使用 Azure AD。 Contoso 用户具有以*contoso.com*结尾的用户主体名称。

在这种情况下，您可以配置两个已连接的组织。 为图形设计机构创建一个连接的组织，为 Contoso 创建一个连接的组织。 如果随后将两个已连接的组织添加到策略中，则具有与策略匹配的用户主体名称的每个组织的用户可以请求访问包。 具有*graphicdesigninstitute.com*域的用户主体名称的用户将与图形设计研究所关联的组织匹配，并允许提交请求。 具有*contoso.com*域的用户主体名称的用户将与 Contoso 连接的组织匹配，并且还允许请求包。 而且，由于图形设计机构使用 Azure AD，因此具有与添加到其租户的[已验证域](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name)匹配的主名称（如*图形设计机构.example）* 的任何用户也能够使用相同的策略请求访问包。

![已连接的组织示例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目录或域中的用户身份验证方式取决于身份验证类型。 已连接组织的身份验证类型包括：

- Azure AD
- [直接联合身份验证](../b2b/direct-federation.md)
- [一次性密码](../b2b/one-time-passcode.md)（域）

有关如何添加连接组织的演示，请观看以下视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>添加连接的组织

要将外部 Azure AD 目录或域添加为已连接的组织，请按照本节中的说明操作。

**先决条件角色**：*全局管理员*、*用户管理员*或*来宾邀请者*

1. 在 Azure 门户中，选择**Azure 活动目录**，然后选择**标识治理**。

1. 在左侧窗格中，选择 **"已连接组织**"，然后选择"**添加已连接的组织**"。

    !["添加已连接的组织"按钮](./media/entitlement-management-organization/connected-organization.png)

1. 选择 **"基本"** 选项卡，然后输入组织的显示名称和说明。

    !["添加已连接的组织"基础知识窗格](./media/entitlement-management-organization/organization-basics.png)

1. 选择 **"目录 + 域**"选项卡，然后选择 **"添加目录 + 域**"。

    将打开"**选择目录 + 域**"窗格。

1. 在搜索框中，输入要搜索 Azure AD 目录或域的域名。 请务必输入整个域名。

1. 验证组织名称和身份验证类型是否正确。 用户登录方式取决于身份验证类型。

    !["选择目录 + 域"窗格](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 选择 **"添加"** 以添加 Azure AD 目录或域。 目前，每个连接的组织只能添加一个 Azure AD 目录或域。

    > [!NOTE]
    > Azure AD 目录或域中的所有用户将能够请求此访问包。 这包括 Azure AD 中来自与目录关联的所有子域的用户，除非 Azure AD 业务阻止到业务 （B2B） 允许或拒绝列表。 有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](../b2b/allow-deny-list.md)。

1. 添加 Azure AD 目录或域后，选择 **"**

    组织将显示在列表中。

    !["目录 + 域"窗格](./media/entitlement-management-organization/organization-directory-domain.png)

1. 选择"**赞助商**"选项卡，然后为此连接的组织添加可选赞助商。

    发起人是目录中已有的内部或外部用户，是与此连接组织的关系的联系人。 内部赞助商是目录中的成员用户。 外部赞助商是以前受邀且已在目录中的已连接组织的来宾用户。 当此连接组织中的用户请求访问此访问包时，可以将赞助商用作审批者。 有关如何邀请来宾用户加入目录的信息，请参阅[在 Azure 门户中添加 Azure 活动目录 B2B 协作用户](../b2b/add-users-administrator.md)。

    当您选择 **"添加/删除**"时，将打开一个窗格，您可以在其中选择内部或外部赞助商。 该窗格显示目录中用户和组未筛选的列表。

    !["赞助商"窗格](./media/entitlement-management-organization/organization-sponsors.png)

1. 选择"**审阅 + 创建**"选项卡，查看组织设置，然后选择"**创建**"。

    !["查看 + 创建"窗格](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>更新已连接的组织 

如果连接的组织更改为其他域、组织的名称更改，或者您希望更改发起人，则可以按照本节中的说明更新已连接的组织。

**先决条件角色**：*全局管理员*、*用户管理员*或*来宾邀请者*

1. 在 Azure 门户中，选择**Azure 活动目录**，然后选择**标识治理**。

1. 在左侧窗格中，选择 **"已连接的组织**"，然后选择已连接的组织以将其打开。

1. 在连接组织的概述窗格中，选择 **"编辑"** 以更改组织名称或说明。  

1. 在 **"目录 + 域**"窗格中，选择 **"更新目录 + 域**"以更改为其他目录或域。

1. 在 **"赞助商"** 窗格中，选择 **"添加内部赞助商**"或 **"添加外部赞助商**"以将用户添加为赞助商。 要删除赞助商，请选择赞助商，并在右侧窗格中选择 **"删除**"。


## <a name="delete-a-connected-organization"></a>删除已连接的组织

如果不再与外部 Azure AD 目录或域有关系，则可以删除已连接的组织。

**先决条件角色**：*全局管理员*、*用户管理员*或*来宾邀请者*

1. 在 Azure 门户中，选择**Azure 活动目录**，然后选择**标识治理**。

1. 在左侧窗格中，选择 **"已连接的组织**"，然后选择已连接的组织以将其打开。

1. 在连接组织的概述窗格中，选择 **"删除**"以将其删除。

    目前，只有在没有连接的用户时，才能删除已连接的组织。

    ![已连接的组织 删除按钮](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>后续步骤

- [管控外部用户的访问权限](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [管理不在目录中的用户的访问](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
