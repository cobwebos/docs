---
title: 快速入门：从 Azure Active Directory (Azure AD) 租户中删除应用程序
description: 本快速入门使用 Azure 门户从 Azure Active Directory (Azure AD) 租户中删除应用程序。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955992"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>快速入门：从 Azure Active Directory (Azure AD) 租户中删除应用程序

本快速入门使用 Azure 门户来删除已添加到 Azure AD 租户的应用程序。

## <a name="prerequisites"></a>先决条件

若要从 Azure AD 租户中删除应用程序，需具备：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- （可选：完成[查看应用](view-applications-portal.md)）。
- （可选：完成[添加应用](add-application-portal.md)）。
- （可选：完成[配置应用](add-application-portal-configure.md)）。
- （可选：完成[设置单一登录](add-application-portal-setup-sso.md)）。

>[!IMPORTANT]
>建议使用非生产环境来测试此快速入门中的步骤。

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>从 Azure AD 租户中删除应用程序

从 Azure AD 租户中删除应用程序的方法如下：

1. 在 Azure AD 门户中，选择“企业应用程序”，然后找到并选择要删除的应用程序。 在这种情况下，我们会删除在前面的快速入门中添加的 GitHub_test 应用程序。
2. 在导航的“管理”部分中，选择“属性”。
3. 选择“删除”，然后选择“是”以确认要从 Azure AD 租户中删除该应用。


## <a name="next-steps"></a>后续步骤

- [应用程序管理最佳做法](application-management-fundamentals.md)
- [应用程序管理常见方案](common-scenarios.md)
- [应用程序管理可见性和控制](cloud-app-security.md)