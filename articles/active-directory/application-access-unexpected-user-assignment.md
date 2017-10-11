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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 916238ba402a2555bac620d7f08e02799d981ae0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-assign-users-to-applications"></a>如何将用户分配给应用程序

本文介绍如何将用户分配给租户中的应用程序。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>如何将用户分配给 Azure AD 中的应用程序？

用户要访问应用程序，必须先以某种方式将其分配给该应用程序。 可使用管理员、业务委托，或有时使用用户本身的身份执行分配。 下文介绍了可以将用户分配给应用程序的方式：

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

9.  [管理员同意应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)以供所有用户，然后选择用户在登录到应用程序

10. 通过登录应用程序，用户自己[同意使用应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](active-directory-enable-sso-scenario.md)
