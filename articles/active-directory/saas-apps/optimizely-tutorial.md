---
title: 教程：Azure Active Directory 与 Optimizely 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Optimizely 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943959"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>教程：Azure Active Directory 与 Optimizely 的集成

本教程介绍如何将 Optimizely 与 Azure Active Directory (Azure AD) 集成。
将 Optimizely 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Optimizely。
* 可让用户使用其 Azure AD 帐户自动登录到 Optimizely（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Optimizely 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Optimizely 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Optimizely 支持 SP 发起的 SSO 

## <a name="adding-optimizely-from-the-gallery"></a>从库中添加 Optimizely

要通过配置将 Optimizely 集成到 Azure AD 中，需从库将 Optimizely 添加到托管式 SaaS 应用的列表中。

**若要从库添加 Optimizely，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Optimizely”，在结果面板中选择“Optimizely”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Optimizely](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Optimizely 的 Azure AD 单一登录  。
若要使单一登录有效，需要在 Azure AD 用户与 Optimizely 相关用户之间建立链接关系。

若要使用 Optimizely 配置和测试 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Optimizely 单一登录](#configure-optimizely-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Optimizely 测试用户](#create-optimizely-test-user)** - 在 Optimizely 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要通过 Optimizely 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Optimizely”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Optimizely 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://app.optimizely.net/<instance name>`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`urn:auth0:optimizely:contoso`

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新该值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. Optimizely 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。  

    ![image](common/edit-attribute.png)

6. 除了上述属性，Optimizely 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | 名称 | 源属性 |
    | ---------------| --------------- |
    | 电子邮件 | user.mail |
    
    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“保存”  。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Optimizely”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-optimizely-single-sign-on"></a>配置 Optimizely 单一登录

1. 若要在“Optimizely”端配置单一登录，请联系 Optimizely 帐户经理，并提供已下载的“证书 (Base64)”和复制的相应 URL   。

2. 在电子邮件回复中，Optimizely 会提供登录 URL（SP 发起的 SSO）和标识符（服务提供商实体 ID）值。

    a. 复制 Optimizely 提供的“SP 发起的 SSO URL”，并粘贴到 Azure 门户中“基本 SAML 配置”部分的“登录 URL”文本框中    。

    b. 复制 Optimizely 提供的“服务提供商实体 ID”，并粘贴到 Azure 门户中“基本 SAML 配置”部分的“登录 URL”文本框中    。

3. 在其他浏览器窗口中，登录 Optimizely 应用程序。

4. 单击右上角的帐户名称，并单击“帐户设置”。 

    ![Azure AD 单一登录](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. 在“帐户”选项卡中，选中“概述”部分“单一登录”下的“启用 SSO”框。  
  
    ![Azure AD 单一登录](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. 单击“保存” 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分需授予 Britta Simon 访问 Optimizely 的权限，使之能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Optimizely”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Optimizely”。 

    ![应用程序列表中的 Optimizely 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-optimizely-test-user"></a>创建 Optimizely 测试用户

本部分需在 Optimizely 中创建名为“Britta Simon”的用户。

1. 在主页上选择“协作者”选项卡  。

2. 单击“新建协作者”  ，向项目添加新协作者。
   
    ![创建 Azure AD 测试用户](./media/optimizely-tutorial/create_aaduser_10.png)

3. 填写电子邮件地址，为其分配角色。 单击“邀请”。 

    ![创建 Azure AD 测试用户](./media/optimizely-tutorial/create_aaduser_11.png)

4. 他们将收到电子邮件邀请。 使用电子邮件地址，需登录到 Optimizely。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Optimizely 磁贴时，应会自动登录到为其设置了 SSO 的 Optimizely。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

