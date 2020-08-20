---
title: 教程：Azure Active Directory 与 New Relic by Account 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 New Relic by Account 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: 19a471b9339828250baa190b1de57c86eb22b30c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>教程：Azure Active Directory 与 New Relic by Account 的单一登录 (SSO) 集成

本教程介绍如何将 New Relic by Account 与 Azure Active Directory (Azure AD) 集成。 将 New Relic by Account 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 New Relic by Account。
* 让用户使用其 Azure AD 帐户自动登录到 New Relic by Account。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 New Relic by Account 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* New Relic by Account 支持 SP 发起的 SSO 

* 配置 New Relic by Account 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-new-relic-by-account-from-the-gallery"></a>从库中添加 New Relic by Account

若要配置 New Relic by Account 与 Azure AD 的集成，需要从库中将 New Relic by Account 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“New Relic by Account”  。 
1. 在结果面板中选择“New Relic by Account”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>配置并测试 New Relic by Account 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 New Relic by Account 的 Azure AD SSO。  若要使 SSO 正常工作，需要在 Azure AD 用户与 New Relic by Account 中的相关用户之间建立链接关系。

若要配置并测试 New Relic by Account 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 New Relic by Account SSO](#configure-new-relic-by-account-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 New Relic by Account 测试用户](#create-new-relic-by-account-test-user)** - 在 New Relic by Account 中创建 Britta Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在“New Relic by Account”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)
1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL  ：

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` - 务必将 `acc_id` 替换为你自己的 New Relic by Account 帐户 ID。

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`rpm.newrelic.com`

1. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 New Relic by Account”部分中，根据你的需要复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

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

在本部分中，你将通过授予 B.Simon 访问 New Relic by Account 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“New Relic by Account”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-new-relic-by-account-sso"></a>配置 New Relic by Account SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 New Relic by Account  公司站点。

2. 在顶部菜单中，单击“帐户设置”。 
   
    ![帐户设置](./media/new-relic-tutorial/ic797036.png "帐户设置")

3. 单击“安全性和身份验证”选项卡，并单击“单一登录”选项卡。  
   
    ![单一登录](./media/new-relic-tutorial/ic797037.png "单一登录")

4. 在 SAML 对话框页上执行以下步骤：
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. 单击“选择文件”，上传已下载的 Azure Active Directory 证书。 

    b. 在“远程登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
   
    c. 在“注销登录 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。  

    d. 单击“保存所做的更改”。 

### <a name="create-new-relic-by-account-test-user"></a>创建 New Relic by Account 测试用户

1. 以管理员身份登录到 New Relic by Account  公司站点。

2. 在顶部菜单中，单击“帐户设置”。 
   
    ![帐户设置](./media/new-relic-tutorial/ic797040.png "帐户设置")

3. 在左侧的“帐户”窗格中，单击“摘要”，并单击“添加用户”。   
   
    ![帐户设置](./media/new-relic-tutorial/ic797041.png "帐户设置")

4. 在“活动用户”对话框中，执行以下步骤： 
   
    ![活动用户](./media/new-relic-tutorial/ic797042.png "活动用户")
   
    a. 在“电子邮件”文本框中，键入要预配的有效 Azure Active Directory 用户的电子邮件地址。 

    b. 选择“用户”作为“角色”。  

    c. 单击“添加此用户”。 

> [!NOTE]
> 可以使用 New Relic by Account 提供的任何其他 New Relic by Account 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“New Relic by Account”磁贴时，应当会自动登录到为其设置了 SSO 的 New Relic by Account。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试在 Azure AD 中使用 New Relic by Account](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)