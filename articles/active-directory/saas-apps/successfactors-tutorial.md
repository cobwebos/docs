---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SuccessFactors 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SuccessFactors 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38d40a2f72e73dde0f99ebbc9701e02c8d03738b
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989489"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SuccessFactors 集成

在本教程中，了解如何将 SuccessFactors 与 Azure Active Directory (Azure AD) 集成。 将 SuccessFactors 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SuccessFactors。
* 让用户使用其 Azure AD 帐户自动登录到 SuccessFactors。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 SuccessFactors 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SuccessFactors 支持 **SP** 发起的 SSO

## <a name="adding-successfactors-from-the-gallery"></a>从库添加 SuccessFactors

要配置 SuccessFactors 与 Azure AD 的集成，需要从库中将 SuccessFactors 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“SuccessFactors”   。
1. 从结果面板中选择“SuccessFactors”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>为 SuccessFactors 配置和测试 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 SuccessFactors 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 SuccessFactors 相关用户之间建立链接关系。

若要为 SuccessFactors 配置和测试 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 SuccessFactors SSO](#configure-successfactors-sso)** - 在应用程序端配置“单一登录”设置。
    1. **[创建 SuccessFactors 测试用户](#create-successfactors-test-user)** - 在 SuccessFactors 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“SuccessFactors”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL  ：

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. 在“标识符”文本框中，使用以下模式键入 URL  ：

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [SuccessFactors 客户端支持团队](https://www.successfactors.com/content/ssf-site/en/support.html)获取这些值。

4. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 SuccessFactors”部分，根据要求复制相应 URL  。

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

在本部分中，通过授予 B.Simon 访问 SuccessFactors 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SuccessFactors”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

    ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-successfactors-sso"></a>配置 SuccessFactors SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 **SuccessFactors 管理门户**。

2. 请访问“应用程序安全性”  并从本机转到“单一登录功能”  。

3. 将任何值放在“重置令牌”  中，然后单击“保存令牌”  以启用 SAML SSO。

    ![在应用端配置单一登录][11]

    > [!NOTE]
    > 此值将用作打开/关闭开关。 如果保存了任何值，则 SAML SSO 为打开。 如果保存为空值，则 SAML SSO 为关闭。

4. 从本机转到以下屏幕快照并执行以下操作：

    ![在应用端配置单一登录][12]
  
    a. 选择“SAML v2 SSO”  单选按钮
  
    b. 设置“SAML 声明方名称”  （例如，SAML 颁发者+公司名称）。

    c. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 对于“需要强制签名”  ，选择“断言”  。

    e. 对于“启用 SAML 标志”  选择“启用”  。

    f. 对于“登录请求签名（SF 生成/SP/RP）”  选择“否”  。

    g. 对于“SAML 配置文件”  ，选择“浏览器/Post 配置文件”  。

    h. 对于“强制证书有效期限”  ，选择“否”  。

    i. 复制从 Azure 门户下载的证书文件的内容，然后将其粘贴到“SAML 验证证书”  文本框。

    > [!NOTE] 
    > 证书内容必须具有开始证书和结束证书标记。

5. 导航到 SAML V2，并执行以下步骤：

    ![在应用端配置单一登录][13]

    a. 对于“支持 SP 启动的全局注销”  ，选择“是”  。

    b. 在“全局注销服务URL (LogoutRequest 目标)”  文本框中，粘贴从 Azure 门户复制的“注销 URL”  值。

    c. 对于“要求 sp 必须加密所有 NameID 元素”  选择“否”  。

    d. 对于“NameID 格式”  ，选择“未指定”  。

    e. 对于“启用 sp 启动的登录 (AuthnRequest)”  选择“是”  。

    f. 在“以公司范围颁发者身份发送请求”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

6. 如果希望登录用户名不区分大小写，请执行这些步骤。

    ![配置单一登录][29]

    a. 请访问“公司设置”  （靠近底部）。

    b. 选中“启用不区分大小写的用户名”  旁边的复选框。

    c. 单击“ **保存**”。

    > [!NOTE]
    > 如果尝试启用此功能，系统会检查是否会创建重复的 SAML 登录名。 例如，如果客户具有用户名 User1 和 user1。 取消区分大小写会认定它们重复。 系统将提供一条错误消息，且不会启用该功能。 客户需要更改其中一个用户名，以使其拼写不同。

### <a name="create-successfactors-test-user"></a>创建 SuccessFactors 测试用户

要使 Azure AD 用户能够登录 SuccessFactors，必须将这些用户预配到 SuccessFactors 中。 对于 SuccessFactors，预配任务需要手动完成。

若要在 SuccessFactors 中创建用户，需要联系 [SuccessFactors 支持团队](https://www.successfactors.com/content/ssf-site/en/support.html)。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Salesforce 磁贴时，应当会自动登录到已设置了 SSO 的 SuccessFactors。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 SuccessFactors](https://aad.portal.azure.com)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
