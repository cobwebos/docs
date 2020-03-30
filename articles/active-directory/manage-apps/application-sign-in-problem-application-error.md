---
title: 登录后，应用页面上将显示错误消息 |微软文档
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185496"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>应用页在用户登录后显示错误消息

在这种情况下，Azure 活动目录 （Azure AD） 会对用户进行签名。 但是，应用程序会显示一条错误消息，并且不会让用户完成登录流。 问题是应用不接受 Azure AD 发出的响应。

应用不接受 Azure AD 的响应的原因有多种。 如果错误消息没有明确标识响应中缺少的内容，请尝试以下操作：

-   如果应用是 Azure AD 库，请验证您是否按照[如何调试 Azure AD 中基于 SAML 的单一登录](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)中的步骤。

-   使用[Fiddler](https://www.telerik.com/fiddler)等工具捕获 SAML 请求、响应和令牌。

-   将 SAML 响应发送给应用供应商，并询问他们缺少什么。

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 响应中缺少属性

要在 Azure AD 配置中添加将在 Azure AD 响应中发送的属性，请按照以下步骤操作：

1. 打开[**Azure 门户**](https://portal.azure.com/)，以全局管理员或共同管理员身份登录。

2. 在左侧导航窗格的顶部，选择 **"所有服务**"以打开 Azure AD 扩展名。

3. 在筛选器搜索框中键入**Azure 活动目录**，然后选择**Azure 活动目录**。

4. 在 Azure AD 导航窗格中选择**企业应用程序**。

5. 选择 **"所有应用程序**"以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用 **"所有应用程序列表**"顶部的 **"筛选器"** 控件。 将 **"显示"** 选项设置为"所有应用程序"。

6. 选择要为单次登录配置的应用程序。

7. 加载应用后，在导航窗格中选择 **"单一登录**"。

8. 在 **"用户属性**"部分中，选择 **"查看并编辑所有其他用户属性**"。 在这里，您可以更改用户登录时要在 SAML 令牌中发送到应用的属性。

   若要添加属性：

   1. 选择 **"添加属性**"。 输入**名称**，并从下拉列表中选择 **"值**"。

   1.  选择“保存”。**** 您将在表中看到新属性。

9. 保存配置。

   下次用户登录到应用时，Azure AD 将在 SAML 响应中发送新属性。

## <a name="the-app-doesnt-identify-the-user"></a>应用不标识用户

登录到应用失败，因为 SAML 响应缺少角色等属性。 或者它失败，因为应用需要**NameID（** 用户标识符）属性的不同格式或值。

如果使用[Azure AD 自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)来创建、维护和删除应用中的用户，请验证用户是否已预配到 SaaS 应用。 有关详细信息，请参阅[未将用户预配到 Azure AD 库应用程序](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>将属性添加到 Azure AD 应用配置

若要更改用户标识符值，请执行以下步骤：

1. 打开[**Azure 门户**](https://portal.azure.com/)，以全局管理员或共同管理员身份登录。

2. 选择左侧导航窗格顶部**的所有服务**以打开 Azure AD 扩展名。

3. 在筛选器搜索框中键入**Azure 活动目录**，然后选择**Azure 活动目录**。

4. 在 Azure AD 导航窗格中选择**企业应用程序**。

5. 选择 **"所有应用程序**"以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用 **"所有应用程序列表**"顶部的 **"筛选器"** 控件。 将 **"显示"** 选项设置为"所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 加载应用后，在导航窗格中选择 **"单一登录**"。

8. 在 **"用户属性**"下，从**用户标识符**下拉列表中选择用户的唯一标识符。

## <a name="change-the-nameid-format"></a>更改 NameID 格式

如果应用程序需要**NameID（** 用户标识符）属性的其他格式，请参阅编辑[nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid)以更改 NameID 格式。

Azure AD 根据所选值或应用在 SAML AuthRequest 中请求的格式选择**NameID**属性（用户标识符）的格式。 有关详细信息，请参阅[单一登录 SAML 协议的](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)"NameID 策略"部分。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>应用需要为 SAML 响应使用不同的签名方法

要更改 SAML 令牌的哪些部分由 Azure AD 进行数字签名，请按照以下步骤操作：

1. 打开[Azure 门户](https://portal.azure.com/)，以全局管理员或共同管理员身份登录。

2. 选择左侧导航窗格顶部**的所有服务**以打开 Azure AD 扩展名。

3. 在筛选器搜索框中键入**Azure 活动目录**，然后选择**Azure 活动目录**。

4. 在 Azure AD 导航窗格中选择**企业应用程序**。

5. 选择 **"所有应用程序**"以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，请使用 **"所有应用程序列表**"顶部的 **"筛选器"** 控件。 将 **"显示"** 选项设置为"所有应用程序"。

6. 选择要为单次登录配置的应用程序。

7. 加载应用程序后，在导航窗格中选择 **"单一登录**"。

8. 在**SAML 签名证书**下，选择 **"显示高级证书签名设置**"。

9. 从以下选项中选择应用期望的 **"签名"** 选项：

   * **签署 SAML 响应**
   * **签署 SAML 响应和断言**
   * **签署 SAML 断言**

   下次用户登录到应用时，Azure AD 将签署所选 SAML 响应的部分。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>应用程序期望SHA-1签名算法

默认情况下，Azure AD 使用最安全的算法对 SAML 令牌进行签名。 我们建议您不要将签名算法更改为*SHA-1，* 除非应用需要 SHA-1。

若要更改签名算法，请执行以下步骤：

1. 打开[Azure 门户](https://portal.azure.com/)，以全局管理员或共同管理员身份登录。

2. 选择左侧导航窗格顶部**的所有服务**以打开 Azure AD 扩展名。

3. 在筛选器搜索框中键入**Azure 活动目录**，然后选择**Azure 活动目录**。

4. 在 Azure AD 导航窗格中选择**企业应用程序**。

5. 选择 **"所有应用程序**"以查看应用程序的列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，请使用 **"所有应用程序列表**"顶部的 **"筛选器"** 控件。 将 **"显示"** 选项设置为"所有应用程序"。

6. 选择要为单次登录配置的应用。

7. 加载应用后，从应用左侧的导航窗格中选择 **"单一登录**"。

8. 在**SAML 签名证书**下，选择 **"显示高级证书签名设置**"。

9. 选择**SHA-1**作为**签名算法**。

   下次用户登录到应用时，Azure AD 将使用 SHA-1 算法对 SAML 令牌进行签名。

## <a name="next-steps"></a>后续步骤
[如何调试 Azure AD 中应用程序中基于 SAML 的单一登录](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)。
