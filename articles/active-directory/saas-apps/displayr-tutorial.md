---
title: 教程：Azure Active Directory 与 Displayr 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Displayr 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103988"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>教程：将 Displayr 与 Azure Active Directory 集成

本教程介绍如何将 Displayr 与 Azure Active Directory (Azure AD) 集成。 将 Displayr 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Displayr。
* 让用户使用其 Azure AD 帐户自动登录到 Displayr。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Displayr 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Displayr 支持 **SP** 发起的 SSO。

## <a name="adding-displayr-from-the-gallery"></a>从库中添加 Displayr

要配置 Displayr 与 Azure AD 的集成，需要从库中将 Displayr 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Displayr”   。
1. 从结果面板中选择“Displayr”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户配置和测试 Displayr 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Displayr 相关用户之间建立链接关系。

若要配置和测试 Displayr 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
2. **[配置 Displayr](#configure-displayr)** ，以在应用程序端配置 SSO 设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Displayr 测试用户](#create-displayr-test-user)** ，以在 Displayr 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
6. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Displayr”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，执行以下步骤：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YOURDOMAIN>.displayr.com`。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`<YOURDOMAIN>.displayr.com` 

    >[!NOTE]
    >这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Displayr 客户端支持团队](mailto:support@displayr.com)获取这些值。 还可参考 Azure 门户的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. Displayr 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

    ![image](common/edit-attribute.png)

1. 除了上述属性，Displayr 应用程序还要求在 SAML 响应中传递回更多的属性。 在“组声明(预览)”对话框中的“用户属性和声明”部分，执行以下步骤   ：

    a. 单击“声明中返回的组”旁边的**笔**。 

    ![image](./media/displayr-tutorial/config04.png)

    ![图像](./media/displayr-tutorial/config05.png)

    b. 从单选列表中选择“所有组”。 

    c. 选择“组 ID”作为“源属性”  。 

    d. 勾选“自定义组声明的名称”。 

    e. 勾选“将组作为角色声明发出”。 

    f. 单击“ **保存**”。

1. 在“设置 Displayr”部分，根据要求复制相应的 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>配置 Displayr

1. 若要在 Displayr 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Displayr”，此时将定向到 Displayr 应用程序  。 在此处，提供管理员凭据以登录到 Displayr。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 如果想要手动设置 Displayr，请打开新的 Web 浏览器窗口并以管理员身份登录 Displayr 公司站点，然后执行以下步骤：

4. 单击“设置”，然后导航到“帐户”。  

    ![配置](./media/displayr-tutorial/config01.png)

5. 在顶部菜单中切换到“设置”，然后在该页中向下滚动，以便单击“配置单一登录(SAML)”   。

    ![配置](./media/displayr-tutorial/config02.png)

6. 在“单一登录(SAML)”  页上，执行以下步骤：

    ![配置](./media/displayr-tutorial/config03.png)

    a. 选中“启用单一登录(SAML)”框。 

    b. 从 Azure AD 的“基本 SAML 配置”部分复制“标识符”实际值，并将其粘贴到“证书颁发者”文本框中    。

    c. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    e. 在记事本中打开“证书(Base64)”文件，复制其内容并将其粘贴到“证书”文本框中  。

    f. “组映射”为可选。 

    g. 单击“ **保存**”。  

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Displayr 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Displayr”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-displayr-test-user"></a>创建 Displayr 测试用户

必须将 Azure AD 用户预配到 Displayr 中，然后他们才能登录到 Displayr。 在 Displayr 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Displayr。

2. 单击“设置”，然后导航到“帐户”。  

    ![Displayr 配置](./media/displayr-tutorial/config01.png)

3. 在顶部菜单中切换到“设置”，在该页中向下滚动到“用户”部分，然后单击“新建用户”    。

    ![Displayr 配置](./media/displayr-tutorial/config07.png)

4. 在“新建用户”页面上，执行以下步骤  ：

    ![Displayr 配置](./media/displayr-tutorial/config06.png)

    a. 在“名称”文本框中，输入用户姓名，例如 Brittasimon   。

    b. 在“电子邮件”文本框中，输入用户的电子邮件，如 `Brittasimon@contoso.com`。 

    c. 选择相应的“组成员身份”。 

    d. 单击“ **保存**”。

### <a name="test-sso"></a>测试 SSO

在访问面板中选择“Displayr”磁贴时，应会自动登录到设置了 SSO 的 Displayr。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
