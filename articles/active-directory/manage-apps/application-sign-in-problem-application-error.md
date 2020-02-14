---
title: 登录后，应用页面上显示错误消息 |Microsoft Docs
description: 当应用返回错误消息时，如何解决 Azure AD 登录的问题。
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185496"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>应用页面在用户登录后显示错误消息

在此方案中，Azure Active Directory （Azure AD）对用户进行签名。 但应用程序将显示一条错误消息，并且不允许用户完成登录流。 问题在于，应用程序不接受 Azure AD 颁发的响应。

应用不接受来自 Azure AD 的响应有几个可能的原因。 如果错误消息未清楚地确定响应中缺少的内容，请尝试以下操作：

-   如果应用是 Azure AD 库，请验证是否遵循[了如何在 Azure AD 中调试对应用程序进行基于 SAML 的单一登录](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)中的步骤。

-   使用[Fiddler](https://www.telerik.com/fiddler)之类的工具来捕获 SAML 请求、响应和令牌。

-   将 SAML 响应发送到应用供应商，并向他们询问丢失的内容。

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 响应中缺少属性

若要在 Azure AD 配置中添加将在 Azure AD 响应中发送的属性，请执行以下步骤：

1. 打开[**Azure 门户**](https://portal.azure.com/)，并以 "全局管理员" 或 "共同管理员" 身份登录。

2. 在左侧导航窗格的顶部，选择 "**所有服务**" 打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** ，然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为其配置单一登录的应用程序。

7. 在应用程序加载后，在导航窗格中选择 "**单一登录**"。

8. 在 "**用户属性**" 部分，选择 "**查看和编辑所有其他用户属性**"。 在这里，你可以更改在用户登录时要发送到 SAML 令牌中的应用的属性。

   若要添加属性：

   1. 选择“添加属性”。 输入**名称**，并从下拉列表中选择**值**。

   1.  选择“保存”。 你将在表中看到新属性。

9. 保存配置。

   用户下次登录到应用时，Azure AD 将在 SAML 响应中发送新的属性。

## <a name="the-app-doesnt-identify-the-user"></a>应用不能识别用户

登录到应用程序失败，因为 SAML 响应缺少某个属性（如角色）。 否则，该应用程序将失败，因为该应用需要具有不同的属性或**值（用户**标识符）特性。

如果使用[Azure AD 自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)在应用中创建、维护和删除用户，请验证是否已将用户预配到 SaaS 应用。 有关详细信息，请参阅[未将用户预配到 Azure AD 库应用程序](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>将属性添加到 Azure AD 应用配置

若要更改用户标识符值，请执行以下步骤：

1. 打开[**Azure 门户**](https://portal.azure.com/)，并以 "全局管理员" 或 "共同管理员" 身份登录。

2. 选择左侧导航窗格顶部的 "**所有服务**" 以打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** ，然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 在应用程序加载后，在导航窗格中选择 "**单一登录**"。

8. 在 "**用户属性**" 下，从 "**用户标识符**" 下拉列表中选择用户的唯一标识符。

## <a name="change-the-nameid-format"></a>更改 NameID 格式

如果应用程序需要另一种格式的**nameid** （用户标识符）特性，请参阅[编辑 NameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid)以更改 nameid 格式。

Azure AD 根据所选值或 SAML AuthRequest 中的应用请求的格式，为**NameID**属性（用户标识符）选择格式。 有关详细信息，请参阅[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)的 "NameIDPolicy" 部分。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>应用需要为 SAML 响应使用不同的签名方法

若要更改 Azure AD 进行数字签名的 SAML 令牌的部分，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)，并以 "全局管理员" 或 "共同管理员" 身份登录。

2. 选择左侧导航窗格顶部的 "**所有服务**" 以打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** ，然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为其配置单一登录的应用程序。

7. 加载应用程序后，在导航窗格中选择 "**单一登录**"。

8. 在 " **SAML 签名证书**" 下，选择 "**显示高级证书签名设置**"。

9. 从以下选项中选择应用期望的**签名选项**：

   * **签名 SAML 响应**
   * **对 SAML 响应和断言进行签名**
   * **签名 SAML 断言**

   用户下次登录到应用时，Azure AD 将对你选择的 SAML 响应部分进行签名。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>应用需要 SHA-1 签名算法

默认情况下，Azure AD 使用最安全的算法对 SAML 令牌进行签名。 建议你不要将签名算法改为*sha-1* ，除非应用需要 sha-1。

若要更改签名算法，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)，并以 "全局管理员" 或 "共同管理员" 身份登录。

2. 选择左侧导航窗格顶部的 "**所有服务**" 以打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** ，然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用程序的列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，请使用 "**所有应用程序" 列表**顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为其配置单一登录的应用。

7. 在应用程序加载后，从应用左侧的导航窗格中选择 "**单一登录**"。

8. 在 " **SAML 签名证书**" 下，选择 "**显示高级证书签名设置**"。

9. 选择 " **sha-1** " 作为**签名算法**。

   用户下次登录到应用时，Azure AD 将使用 SHA-1 算法对 SAML 令牌进行签名。

## <a name="next-steps"></a>后续步骤
[如何在 Azure AD 中调试对应用程序进行基于 SAML 的单一登录](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。
