---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Freshworks 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Freshworks 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377229"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Freshworks 的集成

本教程介绍如何将 Freshworks 与 Azure Active Directory (Azure AD) 集成。 将 Freshworks 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Freshworks。
* 让用户使用其 Azure AD 帐户自动登录到 Freshworks。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Freshworks 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Freshworks 支持 SP 发起的 SSO 

## <a name="adding-freshworks-from-the-gallery"></a>从库中添加 Freshworks

要配置 Freshworks 与 Azure AD 的集成，需要从库中将 Freshworks 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Freshworks”   。
1. 从结果面板中选择“Freshworks”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>配置并测试 Freshworks 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Freshworks 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Freshworks 相关用户之间建立关联。

若要配置和测试 Freshworks 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Freshworks SSO](#configure-freshworks-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Freshworks 测试用户](#create-freshworks-test-user)** - 在 Freshworks 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Freshworks”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.freshworks.com/login` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Freshworks 客户端支持团队](mailto:support@freshworks.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 若要根据要求修改“ **签名**”选项， 请单击“ **编辑**”按钮，打开“ ** SAML 签名证书**” 对话框。 

     ![image](common/edit-certificate.png)

     ![Freshworks 配置](./media/freshworks-tutorial/response.png)

    a. 选择“签署 SAML 响应”作为“签名选项”   。

    b. 单击“ **保存**”。

1. 在“设置 Freshworks”部分中，根据要求复制相应的 URL  。

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

在本部分中，通过授予 B.Simon 访问 Freshworks 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Freshworks”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-freshworks-sso"></a>配置 Freshworks SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 Freshworks 公司站点并执行以下步骤：

2. 在菜单左侧单击“ **安全性**”图标，选择“**单一登录**”选项，然后在“**身份验证方法**”下选择“**SAML SSO**”。 

    ![Freshworks 配置](./media/freshworks-tutorial/configure01.png)

3. 在“单一登录”部分中，执行以下步骤： 

    ![Freshworks 配置](./media/freshworks-tutorial/configure02.png)

    a. 单击“复制”以复制实例的“服务提供商(SP)实体 ID”，将其粘贴到 Azure 门户“基本的 SAML 配置”部分的“标识符(实体 ID)”文本框中。    

    b. 在“IdP 提供的实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 在记事本中打开 Base64 编码的证书，复制其内容，然后将其粘贴到“安全证书”文本框中  。

    e. 单击“ **保存**”。

### <a name="create-freshworks-test-user"></a>创建 Freshworks 测试用户

在本部分，我们将在 Freshworks 中创建名为 B.Simon 的用户。 请与  [Freshworks 客户端支持团队](mailto:support@freshworks.com)协作来在 Freshworks 平台中添加用户。 使用单一登录前，必须先创建并激活用户。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Freshworks 磁贴时，应当会自动登录到你为其设置了 SSO 的 Freshworks。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 BetterWorks](https://aad.portal.azure.com/)

