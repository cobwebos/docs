---
title: 在 Azure AD 权利管理（预览版）中委托任务 - Azure Active Directory
description: 了解在 Azure Active Directory 权利管理中委托任务时可分配的角色。
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
ms.date: 07/10/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b631f078240821e79513c4bd944a33b4725bc52
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207134"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 权利管理（预览版）中委托任务

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

默认情况下，全局管理员与用户管理员可以创建和管理 Azure AD 权利管理的各个方面。 但是，充当这些角色的用户不一定全面了解在哪些情况下需要访问包。 通常，部门中的用户知道哪些人员需要展开协作。 

不要向非管理员授予不受限制的权限，而可以向用户授予执行其作业所需的最低权限，并避免创建有冲突或者不适当的访问权限。 本文介绍在权利管理中委托各种任务时可分配的角色。 

## <a name="delegate-example-for-departmental-adoption"></a>供部门采用的委托示例

为了了解如何在权利管理中委托任务，让我们从一个示例着手。 

假设你的组织中有以下五个用户：

| 用户 | 部门 | 说明 |
| --- | --- | --- |
| Alice | IT | 全局管理员 |
| Bob | 研究 | Bob 也是调查组的主管 |
| Carole | 研究 |  |
| Dave | 市场营销 |  |
| Elisa | 市场营销 | Elisa 也是营销应用部门的主管 |

研究和营销部门都希望对其用户使用权利管理。 其他部门尚未开始使用权利管理，但 Alice 不能等待。 Alice 可按下述方法将任务委托给研究和营销部门。

1. Alice 为目录创建者创建一个新的 Azure AD 安全组，并将 Bob、Carol、Dave 和 Elisa 添加为该组的成员。

1. Alice 使用权利管理设置将该组添加到目录创建者角色。

1. Carol 创建一个“研究”目录，并将 Bob 添加为该目录的共同所有者。 Bob 将自己拥有的研究组作为资源添加到该目录，以便可以在访问包中将其用于研究协作。

1. Dave 创建一个“营销”目录，并将 Elisa 添加为该目录的共同所有者。 Elisa 将自己拥有的营销应用程序作为资源添加到该目录，以便可以在访问包中将其用于营销协作。

现在，研究和营销部门可以利用权利管理。 Bob、Carol、Dave 和 Elisa 可在各自的目录中创建和管理访问包。

![权利管理委托示例](./media/entitlement-management-delegate/elm-delegate.png)


## <a name="entitlement-management-roles"></a>权利管理角色

权利管理具有以下特定的角色。

| Role | 描述 |
| --- | --- |
| 目录创建者 | 创建和管理目录。 通常，该角色是不充当全局管理员的 IT 管理员，或者是资源集合的资源所有者。 创建目录的人员将自动成为该目录的第一个目录所有者，并可以添加其他目录所有者。 |
| 目录所有者 | 编辑和管理现有目录。 通常，该角色是 IT 管理员或资源所有者，或者是目录所有者指定的用户。 |
| 访问包管理者 | 编辑和管理目录中的所有现有访问包。 |

此外，访问包的指定审批者和请求者也拥有权限，但他们不是角色。
 
* 审批者：由策略授权，可以批准或拒绝对访问包的请求，但无法更改访问包定义。
* 请求者：由访问包的策略授权，可以请求该访问包。

下表列出了这些角色可以执行的任务。

| 任务 | 目录创建者 | 目录所有者 | 访问包管理者 | 审批者 |
| --- | :---: | :---: | :---: | :---: |
| [创建新目录](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [编辑目录](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [删除目录](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [将目录所有者或访问包管理者添加到目录](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [在目录中创建新的访问包](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [管理访问包中的资源角色](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [创建和编辑策略](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [直接将用户分配到访问包](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看谁有访问包的分配](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看访问包的请求](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看请求的传递错误](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [取消挂起的请求](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [隐藏访问包](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [删除访问包](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [审批访问请求](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>将资源添加到目录所需的角色

全局管理员可以在目录中添加或删除任何组（云创建的安全组，或云创建的 Office 365 组）、应用程序或 SharePoint Online 站点。 用户管理员可以在目录中添加或删除任何组或应用程序。

对于不充当全局管理员或用户管理员的用户，若要将组、应用程序或 SharePoint Online 站点添加到目录，该用户必须同时具有所需的 Azure AD 目录角色和目录所有者权利管理角色。 下表列出了将资源添加到目录所需的角色组合。 若要从目录中删除资源，必须具有相同的角色。

| Azure AD 目录角色 | 权利管理角色 | 可以添加安全组 | 可以添加 Office 365 组 | 可以添加应用 | 可以添加 SharePoint Online 站点 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全局管理员](../users-groups-roles/directory-assign-admin-roles.md) | 不适用 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [用户管理员](../users-groups-roles/directory-assign-admin-roles.md) | 不适用 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  |  |
| [Teams 服务管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [应用管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  |  | :heavy_check_mark: |  |
| [云应用管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  |  | :heavy_check_mark: |  |
| 用户 | 目录所有者 | 仅限组所有者 | 仅限组所有者 | 仅限应用所有者 |  |

若要确定任务的最小特权角色, 还可以[在 Azure Active Directory 中通过管理任务引用管理员角色](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)。

## <a name="add-a-catalog-creator"></a>添加目录创建者

若要委托目录创建权限，请将用户添加到目录创建者角色。  可以添加单个用户，为方便起见，可以添加一个组，然后，其成员即可创建目录。 遵循以下步骤将用户分配到目录创建者角色。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在“权利管理”部分的左侧菜单中，单击“设置”。

1. 单击“编辑”。

1. 在“委托权利管理”部分，单击“添加目录创建者”，以选择要充当此权利管理角色的成员的用户或组。

1. 单击“选择”。

1. 单击“保存”。

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>添加目录所有者或访问包管理者

若要委托目录或目录中访问包的管理权限，请将用户添加到目录所有者或访问包管理者角色。 创建目录的任何人将成为第一个目录所有者。 遵循以下步骤将用户分配到目录所有者或访问包管理者角色。

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。

1. 在左侧菜单中，单击“角色和管理员”。

1. 单击“添加所有者”或“添加访问包管理者”，以选择这些角色的成员。

1. 单击“选择”以添加这些成员。

## <a name="next-steps"></a>后续步骤

- [添加审批者](entitlement-management-access-package-edit.md#policy-request)
- [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
