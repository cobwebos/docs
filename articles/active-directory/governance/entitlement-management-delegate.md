---
title: 在 Azure AD 授权管理 （预览版）-Azure Active Directory 中的任务委托
description: 了解可以分配委派权利管理 Azure Active Directory 中的任务的角色。
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
ms.openlocfilehash: d4c4933847a39a56084894e5bbd40e166e6b73b6
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798634"
---
# <a name="delegate-tasks-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 授权管理 （预览版） 中的任务委托

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

默认情况下，全局管理员和用户管理员可以创建和管理 Azure AD 授权管理的所有方面。 但是，这些角色中的用户可能不知道访问包是必需的所有方案。 它通常是部门中的用户知道谁需要进行协作。

最少的所需执行其作业并避免创建冲突权限或不适当的访问权限，而不是授予给非管理员的不受限制的权限，您可以授予用户。 本文介绍可以分配委派权利管理中的各种任务的角色。

## <a name="delegate-example-for-departmental-adoption"></a>部门采用委托示例

若要了解如何，您可能委派权利管理中的任务，它有助于考虑这样一个示例。 假设你的组织有以下五个用户：

| 用户 | 部门 | 说明 |
| --- | --- | --- |
| Alice | IT | 全局管理员 |
| Bob | 研究 | Bob 也是信息检索组的所有者 |
| Carole | 研究 |  |
| Dave | Marketing |  |
| Elisa | Marketing | Elisa 也是市场营销应用程序的所有者 |

研究和市场部想要将其用户权利管理。 Alice 尚不可供其他部门，若要使用授权管理。 下面是 Alice 可以将委托到研究与营销部门的任务的一种方法。

1. Alice 创建一个新 Azure AD 安全组目录创建者，并将 Bob、 Carol、 Dave 和 Elisa 添加为该组的成员。

1. Alice 使用授权管理设置将该组添加到目录的创建者角色。

1. 创建 Carol**研究**目录，并将 Bob 添加为共同所有者的目录。 Bob 添加到目录作为资源，他负责研究组，以便它可以访问包中使用，用于研究协作。

1. Dave 需要创建**市场营销**目录，并将 Elisa 添加为共同所有者的目录。 Elisa 添加她拥有目录作为资源，市场营销应用程序，以便它可以访问包中使用，用于市场营销协作。

现在的研究和市场营销部门可以使用授权管理。 Bob 和 Carol、 Dave，和 Elisa 可以创建和管理其各自的目录中访问包。

![授权管理委托示例](./media/entitlement-management-delegate/elm-delegate.png)

## <a name="entitlement-management-roles"></a>授权管理角色

授权管理具有以下特定于授权管理的角色。

| Role | 描述 |
| --- | --- |
| 目录创建者 | 创建和管理目录。 通常 IT 管理员，并且不是全局管理员或资源的集合的资源所有者。 自动创建一个目录的人员将成为目录的第一个目录所有者，并且可以添加其他目录所有者。 |
| 目录所有者 | 编辑和管理现有目录。 通常的 IT 管理员或资源所有者或目录所有者已指定的用户。 |
| 访问包管理器 | 编辑和管理目录中的所有现有访问包。 |

此外，指定审批者和访问包的请求者还有权限，尽管它们不是角色。
 
* 审批者：授权策略来批准或拒绝请求来访问包，但他们无法更改访问包定义。
* 请求者：授权访问包的策略请求该访问权限的包。

下表列出了这些角色可以执行的任务。

| 任务 | 目录创建者 | 目录所有者 | 访问包管理器 | 审批者 |
| --- | :---: | :---: | :---: | :---: |
| [创建一个新的目录](entitlement-management-catalog-create.md) | :heavy_check_mark: |  |  |  |
| [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | | :heavy_check_mark: | | |
| [编辑目录](entitlement-management-catalog-create.md#edit-a-catalog) |  | :heavy_check_mark: |  |  |
| [删除目录](entitlement-management-catalog-create.md#delete-a-catalog) |  | :heavy_check_mark: |  |  |
| [将目录所有者或访问包管理器添加到目录](#add-a-catalog-owner-or-an-access-package-manager) |  | :heavy_check_mark: |  |  |
| [在目录中创建新的访问包](entitlement-management-access-package-create.md) |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [管理访问包中的资源角色](entitlement-management-access-package-edit.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [指定谁可以请求访问包](entitlement-management-access-package-edit.md#add-a-new-policy) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [直接向用户分配访问包](entitlement-management-access-package-edit.md#directly-assign-a-user) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看具有访问包到赋值](entitlement-management-access-package-edit.md#view-who-has-an-assignment) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看访问包的请求](entitlement-management-access-package-edit.md#view-requests) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [查看请求的传递错误](entitlement-management-access-package-edit.md#view-a-requests-delivery-errors) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [取消挂起的请求](entitlement-management-access-package-edit.md#cancel-a-pending-request) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [隐藏访问包](entitlement-management-access-package-edit.md#change-the-hidden-setting) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [删除访问包](entitlement-management-access-package-edit.md#delete) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [批准访问请求](entitlement-management-request-approve.md) |  |  |  | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>所需的角色将资源添加到目录

全局管理员可以添加或删除在目录中的任何组 （云创建安全组或云创建 Office 365 组）、 应用程序或 SharePoint Online 站点。 用户管理员可以添加或删除任何组或应用程序在目录中。

对于用户不是全局管理员或用户管理员，若要将组、 应用程序或 SharePoint Online 网站添加到目录，该用户必须具有*同时*所需 Azure AD 目录角色和目录所有者授权管理角色。 下表列出了所需将资源添加到目录角色组合。 若要从目录中删除资源，必须具有相同的角色。

| Azure AD 目录角色 | 授权管理角色 | 可以添加安全组 | 可以添加 Office 365 组 | 可以添加应用 | 可以添加的 SharePoint Online 网站 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [全局管理员](../users-groups-roles/directory-assign-admin-roles.md) | 不适用 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [用户管理员](../users-groups-roles/directory-assign-admin-roles.md) | 不适用 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  |  |
| [团队服务管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [应用管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  |  | :heavy_check_mark: |  |
| [云应用管理员](../users-groups-roles/directory-assign-admin-roles.md) | 目录所有者 |  |  | :heavy_check_mark: |  |
| 用户 | 目录所有者 | 仅当组所有者 | 仅当组所有者 | 仅当应用程序所有者 |  |

## <a name="add-a-catalog-creator"></a>添加目录创建者

如果你想要委托目录创建，你将用户添加到目录的创建者角色。  可以添加单个用户，或为方便起见可以添加一个组，其成员就能创建目录。 请按照这些步骤将用户分配到目录的创建者角色。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，在**权利管理**部分中，单击**设置**。

1. 单击“编辑”。 

1. 在中**权利管理委派**部分中，单击**添加目录创建者**选择用户或组将成为此权利管理角色的成员。

1. 单击“选择”  。

1. 单击“保存”  。

## <a name="add-a-catalog-owner-or-an-access-package-manager"></a>添加目录所有者或访问包管理器

如果你想要管理目录或访问包目录中的委派，您将用户添加到工作目录的所有者或访问包管理器角色。 任何人创建一个目录将成为第一个目录所有者。 请按照这些步骤将用户分配给目录所有者或访问包管理器角色。

**必备角色：** 用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**，然后打开你想要添加到管理员的目录。

1. 在左侧菜单中，单击**角色和管理员**。

1. 单击**添加所有者**或**访问包管理器添加**若要选择这些角色的成员。

1. 单击**选择**添加这些成员。

## <a name="next-steps"></a>后续步骤

- [添加审批者](entitlement-management-access-package-edit.md#policy-request)
- [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog)
