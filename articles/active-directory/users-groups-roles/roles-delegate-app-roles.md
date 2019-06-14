---
title: 委托应用管理员角色 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中授予权限的应用访问管理委托角色
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ca814551d8c7d309328f236052e1d07ac6f035
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469121"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中委托应用管理员角色

 通过 Azure AD，可以将应用访问管理委托给一组内置管理角色。 除了能减少全局管理员开销外，委托用于管理应用访问任务的专用特权还可以提升安全态势，并降低未经授权访问的可能性。 [在 Azure Active Directory 中委托管理权限](roles-concept-delegation.md)中介绍了委托问题和一般准则。

## <a name="delegate-app-administration"></a>委托应用管理权限

以下角色可授权管理应用注册、单一登录设置、用户和组分配，以及同意委托的权限和应用权限（不包括 Microsoft Graph 和 Azure AD Graph）。 唯一区别是，应用管理员角色还授权管理“应用代理”设置。 任何一个角色都不授权管理“条件访问”设置。
> [!IMPORTANT]
> 分配有此角色的用户可以将凭据添加到应用，并使用这些凭据模拟应用标识。 模拟应用标识可能是一种特权提升（与用户在 Azure AD 中分配有的其他角色相比）。 分配有此角色的用户可能会在模拟应用时创建或更新用户或其他对象。

若要授权在 Azure 门户中管理应用访问，请执行以下操作：

1. 使用符合租户中全局管理员角色条件的帐户登录 [Azure AD 租户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 拥有足够权限后，打开[“角色和管理员”页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)。
3. 打开以下角色之一，以查看它的成员分配：
   * **应用管理员**
   * **云应用管理员**
4. 在角色的“成员”  页上，选择“添加成员”  。
5. 选择一个或多个要添加到角色的成员。 <!--Members can be users or groups.-->

有关这些角色的说明，可以查看[可用角色](directory-assign-admin-roles.md#available-roles)。

## <a name="delegate-app-registration"></a>委托应用注册

默认情况下，所有用户都可以创建应用注册，但你可以有选择地授权创建应用注册或授权同意授权应用。

1. 使用符合租户中全局管理员角色条件的帐户登录 [Azure AD 租户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 拥有足够权限后，执行以下一个或两个设置：
   * 在[租户的“用户设置”页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)上，将“用户可以注册应用”  设置为“否”。
   * 在[企业应用的“用户设置”](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)上，将“用户可以自行同意应用访问公司数据”  设置为“否”。
3. 然后，根据需要，将需要此权限的用户分配为“应用开发人员”角色的成员。

注册应用后，用户自动添加为应用的第一个所有者。

## <a name="delegate-app-ownership"></a>委托应用所有权

应用所有者和应用注册所有者只能分别管理他们拥有的应用程序或应用注册。 例如，如果你添加 Salesforce 应用的所有者，此所有者可以管理对 Salesforce 的访问权限和配置，但无法管理其他任何应用。 一个应用可以有多个所有者，一个用户可以是多个应用的所有者。

应用所有者可以：

* 更改应用属性，如应用请求获取的名称和权限
* 管理凭据
* 配置单一登录
* 分配用户访问权限
* 添加或删除其他所有者
* 编辑应用清单
* 将应用发布到应用库

> [!IMPORTANT]
> 分配有此角色的用户可以将凭据添加到应用，并使用这些凭据模拟应用标识。 模拟应用标识可能是一种特权提升（与用户在 Azure AD 中分配有的其他角色相比）。 分配有此角色的用户可能会在模拟应用时创建或更新用户或其他对象。

应用注册所有者可以查看和编辑应用注册。

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>分配应用所有者的具体步骤

1. 使用符合租户中应用管理员或云应用管理员条件的帐户登录 [Azure AD 租户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 在租户的[“应用注册”页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)上，选择应用，以打开应用的“概览”  页。
3. 选择“所有者”  ，以查看应用所有者列表。
4. 选择“添加”  ，以选择一个或多个要添加到应用的所有者。

### <a name="to-assign-an-owner-to-an-application-registration"></a>分配应用注册所有者的具体步骤

1. 使用符合租户中应用管理员或云应用管理员角色条件的帐户登录 [Azure AD 租户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 拥有足够权限后，在租户的[“企业应用”页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)上，选择应用注册以打开它。
3. 选择“设置”。 
4. 选择“设置”  页上的“所有者”  ，以查看应用所有者列表。
5. 选择“添加所有者”  ，以选择一个或多个要添加到应用的所有者。

## <a name="next-steps"></a>后续步骤

* [Azure AD 管理员角色参考](directory-assign-admin-roles.md)
