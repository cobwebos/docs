---
title: 登录后，应用页上会出现错误消息 |Microsoft Docs
description: 如何解决与 Azure AD 登录应用时返回一条错误消息的问题。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e6a3d0b533dccc3c3111382b014907d5c026ab
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612668"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>用户登录后，应用页面会显示一条错误消息

在此方案中，Azure Active Directory (Azure AD) 让用户登录。 但应用程序显示一条错误消息，不允许用户完成登录流。 问题在于应用程序未接受 Azure AD 发出的响应。

有几个可能的原因，应用程序为何未接受来自 Azure AD 的响应。 如果不清楚地响应中缺少了什么识别错误消息，请尝试以下解决方法：

-   如果 Azure AD 库应用，则验证是否遵循中的步骤[如何在 Azure AD 中调试基于 SAML 的单一登录对应用程序](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。

-   使用之类的工具[Fiddler](https://www.telerik.com/fiddler)捕获 SAML 请求、 响应和令牌。

-   发送到应用程序供应商为 SAML 响应并请求他们缺少的内容。

## <a name="attributes-are-missing-from-the-saml-response"></a>属性是 SAML 响应中缺少

若要将 Azure AD 响应中发送的 Azure AD 配置中添加的属性，请按照下列步骤：

1. 打开[ **Azure 门户**](https://portal.azure.com/)并以全局管理员或共同管理员。

2. 在左侧导航窗格的顶部，选择**所有服务**以打开 Azure AD 扩展。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看您的应用程序的列表。

   > [!NOTE]
   > 如果未看到所需的应用，使用**筛选器**顶部的控件**应用程序列表中所有**。 设置**显示**选项为"所有应用程序。"

6. 选择想要配置为单一登录的应用程序。

7. 应用程序加载后，选择**单一登录**在导航窗格中。

8. 在中**用户属性**部分中，选择**视图和编辑所有其他用户属性**。 可以在此处更改要将用户登录时发送到 SAML 令牌中的应用程序的属性。

   若要添加属性：

   1. 选择“添加属性”。  输入**名称**，然后选择**值**从下拉列表。

   1.  选择**保存**。 你将看到表中的新属性。

9. 保存配置。

   下次用户登录到应用程序中，Azure AD 将在 SAML 响应中发送新的属性。

## <a name="the-app-doesnt-identify-the-user"></a>应用程序不会标识用户

登录到应用程序失败，因为 SAML 响应缺少属性如一个角色。 它会失败，因为该应用程序需要不同的格式或值或**NameID** （用户标识符） 特性。

如果您使用的[Azure AD 自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)要创建，维护和删除应用中的用户，验证对 SaaS 应用程序预配用户。 有关详细信息，请参阅[没有用户预配到 Azure AD 库应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>将属性添加到 Azure AD 应用程序配置

若要更改用户标识符值，请执行以下步骤：

1. 打开[ **Azure 门户**](https://portal.azure.com/)并以全局管理员或共同管理员。

2. 选择**所有服务**左侧和右侧以打开 Azure AD 扩展导航窗格的顶部。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看您的应用程序的列表。

   > [!NOTE]
   > 如果未看到所需的应用，使用**筛选器**顶部的控件**应用程序列表中所有**。 设置**显示**选项为"所有应用程序。"

6. 选择你想要为 SSO 配置的应用。

7. 应用程序加载后，选择**单一登录**在导航窗格中。

8. 下**用户属性**，选择从用户的唯一标识符**用户标识符**下拉列表。

## <a name="change-the-nameid-format"></a>NameID 格式更改

如果应用程序需要其他格式**NameID** （用户标识符） 属性，请参阅[编辑 nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) NameID 格式更改。

Azure AD 中选择的格式**NameID**属性 （用户标识符） 基于选择的值或 SAML AuthRequest 中应用请求的格式。 有关详细信息，请参阅的"NameIDPolicy"部分[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>应用应在 SAML 响应不同的签名方法

若要更改 SAML 令牌中的哪些部分由 Azure AD 进行了数字签名，请按照下列步骤：

1. 打开[Azure 门户](https://portal.azure.com/)并以全局管理员或共同管理员。

2. 选择**所有服务**左侧和右侧以打开 Azure AD 扩展导航窗格的顶部。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看您的应用程序的列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，使用**筛选器**顶部的控件**应用程序列表中所有**。 设置**显示**选项为"所有应用程序。"

6. 选择想要配置为单一登录的应用程序。

7. 应用程序加载后，选择**单一登录**在导航窗格中。

8. 下**SAML 签名证书**，选择**显示高级证书签名设置**。

9. 选择**签名选项**，应用应在从这些选项中进行：

   * **签名 SAML 响应**
   * **签名 SAML 响应和断言**
   * **SAML 断言进行签名**

   下次用户登录到应用程序中，Azure AD 签名 SAML 响应所选的一部分。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>应用程序要求 sha-1 签名算法

默认情况下，Azure AD 签名 SAML 令牌使用最安全的算法。 我们建议你不要更改到的签名算法*sha-1*除非应用程序要求 sha-1。

若要更改签名算法，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)并以全局管理员或共同管理员。

2. 选择**所有服务**左侧和右侧以打开 Azure AD 扩展导航窗格的顶部。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看应用程序的列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，使用**筛选器**顶部的控件**应用程序列表中所有**。 设置**显示**选项为"所有应用程序。"

6. 选择想要配置为单一登录的应用。

7. 应用程序加载后，选择**单一登录**从应用程序的左侧导航窗格。

8. 下**SAML 签名证书**，选择**显示高级证书签名设置**。

9. 选择**sha-1**作为**签名算法**。

   下次用户登录到应用程序中，Azure AD 会使用 sha-1 算法签名的 SAML 令牌。

## <a name="next-steps"></a>后续步骤
[如何在 Azure AD 中调试基于 SAML 的单一登录对应用程序](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。
