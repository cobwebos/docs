---
title: 授权管理中的常见方案 - Azure AD
description: 了解 Azure 活动目录授权管理中常见方案应遵循的高级步骤。
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
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190545"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD 授权管理中的常见方案

可以通过多种方式配置组织的权利管理。 但是，如果您刚刚开始，了解管理员、目录所有者、访问包管理器、审批者和请求者的常见方案会很有帮助。

## <a name="delegate"></a>委托

### <a name="administrator-delegate-management-of-resources"></a>管理员：委托管理资源

1. [观看视频：从 IT 委派到部门经理](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [将用户委派为目录创建者角色](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>目录创建者：委托管理资源

- [“创建新目录”](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>目录所有者：委托管理资源

1. [将共同所有者添加到目录](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [将资源添加到目录](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>目录所有者：访问包的委托管理

1. [观看视频：从目录所有者委派访问包管理器](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [委派用户访问包管理器角色](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>管理组织中用户的访问权限

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>访问包管理器：允许组织中的员工请求访问资源

1. [创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package)
1. [添加组、团队、应用程序或 SharePoint 网站以访问包](entitlement-management-access-package-create.md#resource-roles)
1. [添加请求策略以允许目录中的用户请求访问](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [指定过期设置](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>请求者：请求访问资源

1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查找访问包
1. [请求访问](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>审批人：批准对资源的请求

1. [在“我的访问权限”门户中打开请求](entitlement-management-request-approve.md#open-request)
1. [批准或拒绝访问请求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>请求者：查看您已有权访问的资源

1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看活动访问包

## <a name="govern-access-for-users-outside-your-organization"></a>管理组织外部用户的访问权限

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>管理员：与外部合作伙伴组织协作

1. [了解访问对外部用户的工作原理](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [查看外部用户的设置](entitlement-management-external-users.md#settings-for-external-users)
1. [向外部组织添加连接](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>访问包管理器：与外部合作伙伴组织协作

1. [创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package)
1. [添加组、团队、应用程序或 SharePoint 网站以访问包](entitlement-management-access-package-resources.md#add-resource-roles)
1. [添加请求策略，以允许不在目录中的用户请求访问](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [指定过期设置](entitlement-management-access-package-create.md#lifecycle)
1. [复制链接以请求访问包](entitlement-management-access-package-settings.md)
1. 将链接发送给外部合作伙伴联系人合作伙伴，以便与其用户共享

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>请求者：以外部用户身份请求访问资源

1. 查找从联系人收到的访问包链接
1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [请求访问](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>审批人：批准对资源的请求

1. [在“我的访问权限”门户中打开请求](entitlement-management-request-approve.md#open-request)
1. [批准或拒绝访问请求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>请求者：查看您已有权访问的资源

1. [登录到“我的访问权限”门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看活动访问包

## <a name="day-to-day-management"></a>日常管理

### <a name="access-package-manager-update-the-resources-for-a-project"></a>访问包管理器：更新项目的资源

1. [观看视频：日常管理：情况已经改变](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 打开访问包
1. [添加或删除组、团队、应用程序或 SharePoint 网站](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>访问包管理器：更新项目的持续时间

1. [观看视频：日常管理：情况已经改变](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 打开访问包
1. [打开生命周期设置](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [更新过期设置](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>访问包管理器：更新项目访问审批方式

1. [观看视频：日常管理：情况已经改变](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [打开请求和审批设置的现有策略](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [更新审批设置](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>访问包管理器：更新项目人员

1. [观看视频：日常管理：情况已经改变](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [删除不再需要访问权限的用户](entitlement-management-access-package-assignments.md)
1. [打开请求和审批设置的现有策略](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [添加需要访问权限的用户](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>访问包管理器：将特定用户直接分配给访问包

1. [如果用户需要不同的生命周期设置，则向访问包添加新策略](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [直接将特定用户分配给访问包](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>分配和报告

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>管理员：查看谁分配到访问包

1. 打开访问包
1. [查看分配](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [存档报告和日志](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>管理员：查看分配给用户的资源

1. [查看用户的访问包](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [查看用户的资源分配](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>程序管理

您还可以使用 Microsoft 图形管理访问包、目录、策略、请求和分配。  具有委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有适当角色的用户可以调用[授权管理 API](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)。

## <a name="next-steps"></a>后续步骤

- [委托和角色](entitlement-management-delegate.md)
- [请求过程和电子邮件通知](entitlement-management-process.md)
