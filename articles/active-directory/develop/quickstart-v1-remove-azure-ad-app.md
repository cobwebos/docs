---
title: 从 Azure Active Directory 中删除应用程序
description: 了解如何从 Azure Active Directory (Azure AD) 中删除应用程序。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0038dcc10aafa191121b2797f68d66a3e32b3fa7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207443"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>快速入门：从 Azure Active Directory 中删除应用程序

已经向 Azure Active Directory (Azure AD) 注册了应用程序的企业开发人员和软件即服务 (SaaS) 提供商可能需要从其 Azure AD 租户中删除应用程序的注册。

在本快速入门中，你将学习如何：

* [删除你的组织编写的应用程序](#removing-an-application-authored-by-your-organization)
* [删除其他组织授权的多租户应用程序](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>先决条件

若要开始，你需要有一个其中已注册了应用程序的 Azure AD 租户。

* 如果还没有租户，请[了解如何获取租户](quickstart-create-new-tenant.md)。
* 若要了解如何将应用添加和注册到你的租户，请参阅[将应用添加到 Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)。

## <a name="removing-an-application-authored-by-your-organization"></a>删除组织编写的应用程序

你的组织已注册的应用程序显示在租户的“应用注册”主页上的“我的应用”筛选器下。 这些应用程序是通过 Azure 门户手动注册的，或者通过 PowerShell 或 Microsoft Graph API 以编程方式注册的。 更具体地说，这些应用程序是由租户中的应用程序与服务主体对象表示的。 有关这些对象的详细信息，请参阅[应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>从目录中删除单租户应用程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 在左侧导航窗格中，依次选择“Azure Active Directory”服务、“应用注册”，然后找到并选择想要配置的应用程序。
    此时将转到应用程序的注册主页，并打开应用程序的“设置”页。
1. 在应用程序的注册主页中，选择“删除”。
1. 选择“是”以确认要删除该应用程序。

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>将多租户应用程序从其主目录中删除

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 在左侧导航窗格中，依次选择“Azure Active Directory”服务、“应用注册”，然后找到并选择想要配置的应用程序。
    此时将转到应用程序的注册主页，并打开应用程序的“设置”页。
1. 在“设置”页中，选择“属性”并将“多租户”开关切换为“否”，以便先将应用程序更改为单租户，然后选择“保存”。
    应用程序的服务主体对象会保留在已许可该应用程序的所有组织的租户中。
1. 从应用程序的注册主页中选择“删除”。
1. 选择“是”以确认要删除该应用程序。

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>删除其他组织授权的多租户应用程序

在租户的“应用注册”主页上的“所有应用”筛选器下面显示的一部分应用程序（“我的应用”注册除外）是多租户应用程序。

从技术上讲，这些多租户应用程序来自另一个租户，并已在许可过程中注册到你的租户。 更具体地说，它们仅由租户中的服务主体对象表示，没有相应的应用程序对象。 有关应用程序对象与服务主体对象之间的差别的详细信息，请参阅 [Azure AD 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

若要删除多租户应用程序对目录的访问权限（在授予许可后），公司管理员必须删除该应用程序的服务主体。 管理员必须拥有全局管理员访问权限，并可以通过 Azure 门户或使用 [Azure AD PowerShell Cmdlet](https://go.microsoft.com/fwlink/?LinkId=294151) 删除访问权限。

## <a name="next-steps"></a>后续步骤

了解以下这些与使用 Azure AD v1.0 终结点的应用相关的应用管理快速入门：

- [向 Azure AD 中添加应用程序](quickstart-v1-integrate-apps-with-azure-ad.md)
- [在 Azure AD 中更新应用程序](quickstart-v1-update-azure-ad-app.md)
