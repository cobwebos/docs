---
title: 教程：Azure Active Directory 与 AlertOps 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 AlertOps 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 124f69b3fd6944d0a6e3814f1bbfa5594bcd95cb
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988434"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>教程：将 AlertOps 与 Azure Active Directory 集成

本教程介绍如何将 AlertOps 与 Azure Active Directory (Azure AD) 集成。 将 AlertOps 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AlertOps。
* 让用户使用其 Azure AD 帐户自动登录到 AlertOps。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 AlertOps 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 AlertOps 支持 **SP 和 IDP** 发起的 SSO。

## <a name="adding-alertops-from-the-gallery"></a>从库中添加 AlertOps

若要配置 AlertOps 与 Azure AD 的集成，需要从库中将 AlertOps 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“AlertOps”   。
1. 从结果面板中选择“AlertOps”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **Britta Simon** 的测试用户为 AlertOps 配置和测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 AlertOps 相关用户之间建立关联。

若要为 AlertOps 配置和测试 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
2. **[配置 AlertOps](#configure-alertops)** ，以便在应用程序端配置 SSO 设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 AlertOps 测试用户](#create-alertops-test-user)** ，以便在 AlertOps 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“AlertOps”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    1. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.alertops.com`

    1. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.alertops.com/login.aspx`

1. 如果要在 SP  发起的模式下配置应用程序，请点击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [AlertOps 客户端支持团队](mailto:support@alertops.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 AlertOps”部分，根据要求复制相应的 URL。 

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>配置 AlertOps

1. 若要在 AlertOps 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 AlertOps”  会将你定向到 AlertOps 应用程序。 在此处，请提供管理员凭据以登录到 AlertOps。 浏览器扩展会自动配置应用程序，并自动执行第 3 步到第 5 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 AlertOps，请打开新的 Web 浏览器窗口，以管理员身份登录 AlertOps 公司站点，并执行以下步骤：

4. 在左侧导航面板中单击“帐户设置”  。

    ![AlertOps 配置](./media/alertops-tutorial/configure1.png)

5. 在“订阅设置”页上，选择“SSO”并执行以下步骤：  

    ![AlertOps 配置](./media/alertops-tutorial/configure2.png)

    a. 选中“使用单一登录(SSO)”复选框。 

    b. 从下拉列表中选择“Azure Active Directory”作为“SSO 提供程序”。  

    c. 在“颁发者 URL”文本框中，填写在 Azure 门户的“基本 SAML 配置”部分使用的标识符值。  

    d. 在“SAML 终结点 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    e. 在“SLO 终结点 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    f. 从下拉列表中选择“SHA256”作为“SAML 签名算法”。  

    g. 在记事本中打开已下载的 Certificate(Base64) 文件。 将其内容复制到剪贴板，然后粘贴到“X.509 证书”文本框中。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 AlertOps 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“AlertOps”。 
1. 在应用的概览页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-alertops-test-user"></a>创建 AlertOps 测试用户

1. 在另一个浏览器窗口中，以管理员身份登录到 AlertOps 公司站点。

2. 在左侧导航面板中单击“用户”。 

    ![AlertOps 配置](./media/alertops-tutorial/user1.png)

3. 选择“添加用户”。 

    ![AlertOps 配置](./media/alertops-tutorial/user2.png)

4. 在“添加用户”对话框中，执行以下步骤： 

    ![AlertOps 配置](./media/alertops-tutorial/user3.png)

    a. 在“登录用户名”文本框中输入用户的用户名，例如 **Brittasimon**。 

    b. 在“正式电子邮件”文本框中，输入用户的电子邮件地址，例如 **Brittasimon\@contoso.com**。 

    c. 在“名字”文本框中，输入用户的名字（如“Britta”）   。

    d. 在“姓氏”文本框中，输入用户的姓氏，例如 **Simon**。 

    e. 根据组织的情况，从下拉列表中选择“类型”值。 

    f. 根据组织的情况，从下拉列表中选择用户的“角色”。 

    g. 选择 **添加** 。

### <a name="test-sso"></a>测试 SSO

在访问面板中选择“AlertOps”磁贴时，应会自动登录到设置了 SSO 的 AlertOps。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)