---
title: 教程：Azure Active Directory 与 Freedcamp 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Freedcamp 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101917"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>教程：将 Freedcamp 与 Azure Active Directory 集成

本教程介绍如何将 Freedcamp 与 Azure Active Directory (Azure AD) 集成。 将 Freedcamp 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Freedcamp。
* 让用户使用其 Azure AD 帐户自动登录到 Freedcamp。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Freedcamp 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Freedcamp 支持“SP 和 IDP”发起的 SSO  。

## <a name="adding-freedcamp-from-the-gallery"></a>从库中添加 Freedcamp

要配置 Freedcamp 与 Azure AD 的集成，需要从库中将 Freedcamp 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Freedcamp”   。
1. 从结果面板中选择“Freedcamp”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户为 Freedcamp 配置和测试 Azure AD SSO  。 若要使 SSO 有效，需要在 Azure AD 用户与 Freedcamp 相关用户之间建立关联。

若要配置和测试 Freedcamp 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
2. [配置 Freedcamp](#configure-freedcamp)，以便在应用程序端配置 SSO 设置  。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Freedcamp 测试用户](#create-freedcamp-test-user)** - 在 Freedcamp 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Freedcamp”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    1. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. 如果要在 SP  发起的模式下配置应用程序，请点击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 用户还可以输入与自己的客户域相关的 URL 值，且不一定采用 `freedcamp.com` 模式，可以输入特定于其应用程序实例的任何客户域特定值。 此外，可以联系 [Freedcamp 客户支持团队](mailto:devops@freedcamp.com)，了解有关 url 模式的更多信息。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Freedcamp”部分，根据要求复制相应的 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>配置 Freedcamp

1. 若要在“Freedcamp”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Freedcamp”会将你定向到 Freedcamp 应用程序  。 在这里，提供管理员凭据，登录到 Freedcamp。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-5。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Freedcamp，请打开新的 Web 浏览器窗口，以管理员身份登录 Freedcamp 公司网站，并执行以下步骤：

4. 在页面的右上角，点击“个人资料”，然后导航至“我的帐户”   。

    ![Freedcamp 配置](./media/freedcamp-tutorial/config01.png)

5. 在菜单栏的左侧，单击“SSO”，然后在“SSO 连接”页面上执行以下步骤   ：

    ![Freedcamp 配置](./media/freedcamp-tutorial/config02.png)

    a. 在“标题”文本框中，键入标题  。

    b. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 在记事本中打开 Base64 编码的证书，复制其内容，然后将其粘贴到“证书”文本框中  。

    e. 单击“提交”  。

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

在本部分，我们通过授予 Britta Simon 访问 Freedcamp 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Freedcamp”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-freedcamp-test-user"></a>创建 Freedcamp 测试用户

必须将 Azure AD 用户预配到 Freedcamp 中，然后他们才能登录到 Freedcamp。 在 Freedcamp 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Freedcamp。

2. 在页面的右上角，点击“个人资料”，然后导航至“管理系统”   。

    ![Freedcamp 配置](./media/freedcamp-tutorial/config03.png)

3. 在“管理系统”页面的右侧，执行以下步骤：

    ![Freedcamp 配置](./media/freedcamp-tutorial/config04.png)

    a. 单击“添加或邀请用户”  。

    b. 在“电子邮件”文本框中，输入用户的电子邮件，例如 `Brittasimon@contoso.com`  。

    c. 单击“添加用户”  。

### <a name="test-sso"></a>测试 SSO

在访问面板中选择“Freedcamp”磁贴时，应会自动登录到设置了 SSO 的 Freedcamp。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)