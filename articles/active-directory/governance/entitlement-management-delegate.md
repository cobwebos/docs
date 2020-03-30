---
title: 授权和角色在授权管理 - Azure AD
description: 了解如何将访问治理从 IT 管理员委派给部门经理和项目经理，以便他们可以自行管理访问权限。
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261835"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 授权管理中的委派和角色

默认情况下，全局管理员与用户管理员可以创建和管理 Azure AD 权利管理的各个方面。 但是，这些角色中的用户可能不知道需要访问包的所有情况。 通常，是各个部门、团队或项目中的用户知道他们正在与谁协作、使用什么资源以及多长时间。 不要向非管理员授予不受限制的权限，而可以向用户授予执行其作业所需的最低权限，并避免创建有冲突或者不适当的访问权限。

本视频概述了如何将访问治理从 IT 管理员委派给不是管理员的用户。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>委托示例

要了解如何在授权管理中委派访问治理，考虑一个示例很有帮助。 假设您的组织具有以下管理员和经理。

![从 IT 管理员委派给经理](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

作为 IT 管理员，Hana 在每个部门都有联系人 -- -- 市场营销部的 Mamta、财务部的 Mark 和法律部的 Joe，负责其部门的资源和业务关键内容。

通过授权管理，您可以将访问治理委派给这些非管理员，因为他们知道哪些用户需要访问、访问多长时间以及哪些资源。 这可确保合适的人员管理其部门的访问。

以下是 Hana 将访问治理委派给营销、财务和法律部门的一种方式。

1. Hana 创建了一个新的 Azure AD 安全组，并将妈妈、Mark 和 Joe 添加为该组的成员。

1. Hana 将该组添加到目录创建者角色。

    Mamta、Mark 和 Joe 现在可以为其部门创建目录，添加其部门所需的资源，并在目录中执行进一步的委派。

    请注意，妈妈、马克和 Joe 看不到彼此的目录。

1. Mamta 创建**一个市场营销**目录，它是一个资源容器。

1. Mamta 将营销部门拥有的资源添加到此目录中。

1. Mamta 可以从她的部门添加其他人作为此目录的目录所有者。 这有助于共享目录管理职责。

1. Mamta 可以进一步将市场营销目录中的访问包的创建和管理委托给营销部门的项目经理。 她可以通过将它们分配给访问包管理器角色来执行此操作。 访问包管理器可以创建和管理访问包。 

下图显示了包含市场营销、财务和法律部门资源的目录。 使用这些目录，项目经理可以为其团队或项目创建访问包。

![权利管理委托示例](./media/entitlement-management-delegate/elm-delegate.png)

委派后，营销部门可能具有类似于下表的角色。

| 用户 | 工作角色 | Azure AD 角色 | 权利管理角色 |
| --- | --- | --- | --- |
| 汉娜 | IT 管理员 | 全局管理员或用户管理员 |  |
| 马姆塔 | 营销经理 | 用户 | 目录创建者和目录所有者 |
| Bob | 营销主管 | 用户 | 目录所有者 |
| 杰西卡 | 营销项目经理 | 用户 | 访问包管理者 |

## <a name="entitlement-management-roles"></a>权利管理角色

权利管理具有以下特定的角色。

| 权利管理角色 | 描述 |
| --- | --- |
| 目录创建者 | 创建和管理目录。 通常，该角色是不充当全局管理员的 IT 管理员，或者是资源集合的资源所有者。 创建目录的人员将自动成为该目录的第一个目录所有者，并可以添加其他目录所有者。 目录创建者无法管理或查看他们不拥有的目录，也不能将他们不拥有的资源添加到目录中。 如果目录创建者需要管理其他目录或添加他们不拥有的资源，他们可以请求成为该目录或资源的共同所有者。 |
| 目录所有者 | 编辑和管理现有目录。 通常，该角色是 IT 管理员或资源所有者，或者是目录所有者指定的用户。 |
| 访问包管理者 | 编辑和管理目录中的所有现有访问包。 |

此外，访问包的指定审批者和请求者也拥有权限，但他们不是角色。

| Right | 描述 |
| --- | --- |
| 审批者 | 由策略授权，可以批准或拒绝对访问包的请求，但无法更改访问包定义。 |
| 请求者 | 由访问包的策略授权，可以请求该访问包。 |

下表列出了授权管理角色可以执行的任务。

| 任务 | 管理员 | 目录创建者 | 目录所有者 | 访问包管理者 |
| --- | :---: | :---: | :---: | :---: |
| [委托给目录创建者](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [添加连接的组织](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [“创建新目录”](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [添加目录所有者](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [编辑目录](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [删除目录](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [委托给访问包管理器](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [删除访问包管理器](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [在目录中创建新的访问包](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [更改访问包中的资源角色](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [创建和编辑策略](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [直接将用户分配到访问包](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看谁有访问包的分配](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看访问包的请求](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [查看请求的传递错误](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [重新处理请求](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [取消挂起的请求](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [隐藏访问包](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [删除访问包](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>将资源添加到目录所需的角色

全局管理员可以在目录中添加或删除任何组（云创建的安全组或云创建的 Office 365 组）、应用程序或 SharePoint Online 网站。 用户管理员可以在目录中添加或删除任何组或应用程序。

对于不充当全局管理员或用户管理员的用户，若要将组、应用程序或 SharePoint Online 站点添加到目录，该用户必须同时具有所需的 Azure AD 目录角色和目录所有者权利管理角色。** 下表列出了将资源添加到目录所需的角色组合。 若要从目录中删除资源，必须具有相同的角色。

| Azure AD 目录角色 | 权利管理角色 | 可以添加安全组 | 可以添加 Office 365 组 | 可以添加应用 | 可以添加 SharePoint Online 站点 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全局管理员](../users-groups-roles/directory-assign-admin-roles.md) | 不适用 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [用户管理员](../users-groups-roles/directory-assign-admin-roles.md) | 不适用 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  |  |
| [团队服务管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  |  | :heavy_check_mark: |  |
| [云应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  |  | :heavy_check_mark: |  |
| 用户 | 目录所有者 | 仅限组所有者 | 仅限组所有者 | 仅限应用所有者 |  |

若要确定任务的最小特权角色，还可以参考[按 Azure Active Directory 中的管理员任务划分的管理员角色](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)。

## <a name="next-steps"></a>后续步骤

- [将访问治理委派给目录创建者](entitlement-management-delegate-catalog.md)
- [创建和管理资源目录](entitlement-management-catalog-create.md)
