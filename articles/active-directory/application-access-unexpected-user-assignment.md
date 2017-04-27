---
title: "如何将用户分配给应用程序 | Microsoft Docs"
description: "了解如何将用户分配给租户中的应用程序"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 43ed4b0e96c583d8fd9da57eec40ddd2e96fee2f
ms.lasthandoff: 04/11/2017


---

# <a name="how-to-assign-users-to-applications"></a>如何将用户分配给应用程序

本文介绍如何将用户分配给租户中的应用程序。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何将用户分配给 Azure AD 中的应用程序？

用户若要访问应用程序，必须先以某种方式将其分配给该应用程序。 可使用管理员、业务委托，或有时使用用户本身的身份执行分配。 下文介绍了可以将用户分配给应用程序的方式：

1.  管理员直接[将用户分配给](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)应用程序

2.  管理员[将用户是其成员的组分配](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)给应用程序，包括：

  * 已从本地同步的组

  * 在云中创建的静态安全组

  * 在云中创建的[动态安全组](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

  * 在云中创建的 Office 365 组

  * [所有用户](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)组

3.  管理员启用[自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)以允许用户**无需业务批准**，即可使用[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)的“添加应用”功能添加应用程序

4.  管理员启用[自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)以允许用户在经过**选定业务批准者的事先批准**的情况下，使用[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)的“添加应用”功能添加应用程序

5.  管理员启用[自助服务组管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)以允许用户**无需业务批准**，即可加入已对其分配应用程序的组

6.  管理员启用[自助服务组管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)以允许用户在经过**选定业务批准者的事先批准**的情况下，加入已对其分配应用程序的组

7.  对于第一方应用程序，如 [ Microsoft Office 365](http://products.office.com/)，管理员直接为用户分配许可证

8.  对于第一方应用程序，如 [ Microsoft Office 365](http://products.office.com/)，管理员直接为用户是其成员的组分配许可证

9.  [管理员同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)所有用户均可使用某个应用程序，然后用户登录该应用程序

10. 通过登录应用程序，用户自己[同意使用应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](active-directory-enable-sso-scenario.md)

