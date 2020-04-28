---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 FCM HUB 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 FCM HUB 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f3a9436c-8f33-462a-950b-8eb57ec31119
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/25/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5abc1e093dc7d4e8da823227dc3e3caa556e37e4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406851"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fcm-hub"></a>教程：Azure Active Directory 单一登录 (SSO) 与 FCM HUB 的集成

本教程介绍如何将 FCM HUB 与 Azure Active Directory (Azure AD) 集成。 将 FCM HUB 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 FCM HUB。
* 让用户使用其 Azure AD 帐户自动登录到 FCM HUB。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 FCM HUB 订阅。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* FCM HUB 支持 SP 和 IDP 发起的 SSO 
* 配置 FCM HUB 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-fcm-hub-from-the-gallery"></a>从库中添加 FCM HUB

若要配置 FCM HUB 与 Azure AD 的集成，需要从库中将 FCM HUB 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 FCM HUB。  
1. 在结果面板中选择“FCM HUB”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-fcm-hub"></a>为 FCM HUB 配置并测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 FCM HUB 的 Azure AD SSO。  若要使 SSO 有效，需要在 Azure AD 用户与 FCM HUB 相关用户之间建立关联。

若要配置并测试 FCM HUB 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 FCM HUB SSO](#configure-fcm-hub-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 FCM HUB 测试用户](#create-fcm-hub-test-user)** - 在 FCM HUB 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“FCM HUB”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://hub.fcm.travel/SsoSp/SpInit?clientid=<CUSTOMID>`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 若要获取该值，请联系为你指派的客户经理，或者联系 [FCM HUB 客户端支持团队](mailto:fcmssoadmin@us.fcm.travel)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 选择“保存”。 

1. 在“管理声明”  页的“用户属性和声明”  部分中，添加以下自定义属性：
   - **Name**：PortalID
   - **源**：Attribute
   - **源属性**：PortalID（FCM 提供的值）

1. 在“SAML 签名证书”  部分中，使用编辑选项选择或输入以下设置，然后选择“保存”  ：
   - **签名选项**：对 SAML 响应进行签名
   - **签名算法**：SHA-256

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 FCM HUB”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 FCM HUB 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“FCM HUB”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-fcm-hub-sso"></a>配置 FCM HUB SSO

若要在 FCM HUB 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给我们为你指派的负责提供支持的客户经理，或联系 [FCM HUB 客户端支持团队](mailto:fcmssoadmin@us.fcm.travel)。   他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-fcm-hub-test-user"></a>创建 FCM HUB 测试用户

在本部分，你将在 FCM HUB 中创建名为 B.Simon 的用户。 联系客户经理或 [FCM HUB 客户端支持团队](mailto:fcmssoadmin@us.fcm.travel)，将用户添加到 FCM HUB 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“FCM HUB”磁贴时，应会自动登录到设置了 SSO 的 FCM HUB。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 FCM HUB](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 FCM HUB](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
