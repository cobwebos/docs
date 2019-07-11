---
title: 从 Azure AD 应用库配置应用程序一键式 SSO | Microsoft Docs
description: 从 Azure AD 应用库配置应用程序一键式 SSO 的步骤。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358240823da469551e254356fc0613bea20d78c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064811"
---
# <a name="one-click-sso-feature-for-azure-ad-gallery-applications"></a>Azure AD 库应用程序的一键式 SSO 功能

 本教程介绍如何针对提供 UI 进行 SSO 配置的所有 SAML 应用程序执行一键式 SSO。

## <a name="introduction-to-one-click-sso"></a>一键式 SSO 简介

我们已引入一键式 SSO 功能，以便为支持 SAML 协议的 Azure AD 库应用配置单一登录。 我们已在 Azure AD SSO 配置页上提供此选项，使客户能够在应用程序端自动配置 Azure AD 元数据。 目的是帮助客户花费少量的精力快速设置 SSO。 

## <a name="advantages-of-the-one-click-sso"></a>一键式 SSO 的优势

- 当客户需要在应用程序端执行手动设置时，一键式 SSO 可以快速完成库应用程序的 SSO 配置。
- 更有效、更准确的配置。
- 无需与合作伙伴沟通，无需请求设置方面的支持，因为应用程序提供了 UI 用于 SAML 配置。

## <a name="prerequisites"></a>先决条件

- 已获取应用程序的有效订阅，以及配置一键式 SSO 时所需的管理员凭据。
- 已在浏览器中安装 Microsoft 提供的 **“我的应用”安全登录浏览器扩展**。 若要详细了解此扩展，请参阅[此链接](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

## <a name="one-click-sso-feature-step-by-step-details"></a>一键式 SSO 功能用法详解

1. 从 Azure AD 应用库添加应用程序。

2. 单击“单一登录”。

3. 单击“启用单一登录”。

4. 在“基本 SAML 配置”部分填充必需的配置值。

    > [!NOTE] 
    > 如果应用程序需要配置自定义声明，请在执行一键式 SSO 之前配置这些声明。

5. 如果针对任何库应用程序实施了一键式 SSO 功能，则会看到以下屏幕。 如果尚未安装 **“我的应用”安全登录浏览器扩展**，需要单击“安装扩展”选项。 

    ![安装“我的应用”安全登录浏览器扩展](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. 将该扩展添加到浏览器后，单击“设置应用程序名称”；随后你会重定向到应用程序管理门户。  需要以管理员身份登录才能进入应用程序。

    ![设置应用程序名称](./media/one-click-sso-tutorial/setup-sso.png)

7. 现在，该浏览器扩展会自动为你配置应用程序。 它首先要求你确认是否继续。 单击 **“是”** 。

    ![保存自动填充的数据](./media/one-click-sso-tutorial/save-autopopulate.png)

    > [!NOTE]
    > 如有任何应用程序需要执行额外的导航或步骤，你应会看到相应的消息，要求你执行这些步骤。 

8. 完成配置后，单击“确定”保存更改。 

    ![保存自动填充的数据](./media/one-click-sso-tutorial/save-data.png)

9. 此时会弹出一条成功确认消息。现已成功配置 SSO 设置。 然后，可对应用程序进行测试。

    ![已配置 SSO](./media/one-click-sso-tutorial/sso-configured.png)

10. 成功完成配置后，应用程序将会注销，你将返回到 Azure 门户。

11. 可以单击“测试”按钮测试单一登录。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [什么是“我的应用”安全登录浏览器扩展](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 