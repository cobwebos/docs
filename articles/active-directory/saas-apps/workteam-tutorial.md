---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Workteam 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workteam 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b14c08604fcc0f6d2550127ca1f1aa053172b75
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Workteam 集成

本教程介绍如何将 Workteam 与 Azure Active Directory (Azure AD) 集成。 将 Workteam 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Workteam。
* 让用户使用其 Azure AD 帐户自动登录到 Workteam。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Workteam 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Workteam 支持 SP 和 IDP 发起的 SSO 

## <a name="adding-workteam-from-the-gallery"></a>从库中添加 Workteam

要配置 Workteam 与 Azure AD 的集成，需要从库中将 Workteam 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Workteam”   。
1. 从结果面板中选择“Workteam”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>配置并测试 Workteam 的 Azure AD 单一登录

使用名为 B.Simon  的测试用户配置并测试 Workteam 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Workteam 相关用户之间建立链接关系。

若要配置和测试 Workteam 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Workteam SSO](#configure-workteam-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Workteam 测试用户](#create-workteam-test-user)** - 在 Workteam 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Workteam”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置  部分，应用程序在 **IDP** 发起的模块中进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”  按钮来保存配置。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.workte.am` 

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Workteam”部分，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 Workteam 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Workteam”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="configure-workteam-sso"></a>配置 Workteam SSO

1. 若要在 Workteam 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Workteam”  会定向到 Workteam 应用程序。 在此处，请提供管理员凭据以登录到 Workteam。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Workteam，请打开新的 Web 浏览器窗口，以管理员身份登录 Workteam 公司站点，并执行以下步骤：

4. 在右上角单击**个人资料徽标**，然后单击“组织设置”。  

    ![Workteam 设置](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. 在“身份验证”部分下，单击“设置”徽标。  

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. 在“SAML 设置”  页上，执行以下步骤：

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. 为“SAML IdP”选择“AD Azure”。  

    b. 在“SAML 单一登录服务 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“SAML 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在记事本中，打开从 Azure 门户下载的 base-64 编码证书，复制其内容，然后将其粘贴到“SAML 签名证书(Base64)”框中   。

    e. 单击“确定”。 

### <a name="create-workteam-test-user"></a>创建 Workteam 测试用户

为了使 Azure AD 用户能够登录 Workteam，必须对其进行预配才能使其登录 Workteam。 在 Workteam 中，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Workteam。

2. 在“组织设置”页的中上部分，依次单击“用户”、“新建用户”。   

    ![Workteam 用户](./media/workteam-tutorial/tutorial_workteam_user.png)

3. 在“新建员工”页上执行以下步骤： 

    ![Workteam 新用户](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. 在“姓名”文本框中，输入用户的名字，例如 **B.Simon**。 

    b. 在“电子邮件”文本框中，输入用户的电子邮件，如 `B.Simon\@contoso.com`。 

    c. 单击“确定”。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Workteam 磁贴时，应当会自动登录到你为其设置了 SSO 的 Workteam。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Workteam](https://aad.portal.azure.com/)

