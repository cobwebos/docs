---
title: 教程：Azure Active Directory 与 LearnUpon 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LearnUpon 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdcd8c6024b3cacb422b556718bbbdbb5d601c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098245"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>教程：Azure Active Directory 与 LearnUpon 的集成

在本教程中，了解如何将 LearnUpon 与 Azure Active Directory (Azure AD) 集成。
将 LearnUpon 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 LearnUpon。
* 可让用户使用其 Azure AD 帐户自动登录到 LearnUpon（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 LearnUpon 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 LearnUpon 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。


* LearnUpon 支持 IDP 发起的 SSO 

* LearnUpon 支持“实时”用户预配 


## <a name="adding-learnupon-from-the-gallery"></a>从库中添加 LearnUpon

要配置 LearnUpon 与 Azure AD 的集成，需要从库中将 LearnUpon 添加到托管 SaaS 应用列表。

**若要从库中添加 LearnUpon，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“LearnUpon”  ，从结果面板中选择“LearnUpon”  ，再单击“添加”  按钮，以添加应用程序。

    ![结果列表中的 LearnUpon](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 LearnUpon 的 Azure AD 单一登录  。
若要使单一登录有效，需要在 Azure AD 用户与 LearnUpon 相关用户之间建立链接关系。

若要配置和测试 LearnUpon 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 LearnUpon 单一登录](#configure-learnupon-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 LearnUpon 测试用户](#create-learnupon-test-user)** - 在 LearnUpon 中创建 Britta Simon 的对应用户，并将它与用户的 Azure AD 身份关联。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 LearnUpon 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 LearnUpon 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![LearnUpon 域和 URL 单一登录信息](common/idp-reply.png)

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE]
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 若要获取此值，请与 [LearnUpon 客户端支持团队](https://www.learnupon.com/features/support/)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页上找到“指纹”   - 这将添加到 LearnUpon SAML 设置。

    ![证书下载链接](common/certificateraw.png)

6. 在“设置 LearnUpon”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-learnupon-single-sign-on"></a>配置 LearnUpon 单一登录

1. 打开另一个浏览器实例并使用管理员帐户登录到 LearnUpon。

1. 单击“设置”选项卡。 

    ![配置单一登录](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. 单击“单一登录 - SAML”，并单击“常规设置”以配置 SAML 设置。  
   
    ![配置单一登录](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. 在“常规设置”  部分中，执行以下步骤：
   
    ![配置单一登录](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. 选择“启用”。 

    b. 将“版本”  选择为“2.0”  。

    c. 将“跳过条件”  选择为“否”  。

    d. 在“SAML 令牌 Post 参数名称”文本框中，键入上面指定的 SAML 使用者 URL 的请求 post 参数的名称（例如 **SAMLResponse**），该参数包含要验证和进行身份验证的 SAML 断言。

    e. 在“名称标识符格式”  文本框中，键入相应的值以指明用户标识符（电子邮件）在 SAML 断言中所处的位置，例如 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。
  
    f. 在“标识符提供程序位置”  文本框中，键入相应的值，指明当用户在 Azure 门户登录屏幕上单击你已上传的图标时要将用户发送到何处。
  
    g. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    h. 单击“管理指纹”，并上载所下载的证书的指纹。 

1. 单击“用户设置”  ，并执行以下步骤：

     ![配置单一登录](./media/learnupon-tutorial/tutorial_learnupon_11.png)  

    a. 在“名字标识符格式”文本框中  ，键入相应的值，指明用户名字在 SAML 断言中所处的位置，例如：`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。
  
    b. 在“姓氏标识符格式”  文本框中，键入相应的值，指明用户姓氏在 SAML 断言中所处的位置，例如：`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 LearnUpon 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“LearnUpon”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“LearnUpon”。 

    ![应用程序列表中的 LearnUpon 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-learnupon-test-user"></a>创建 LearnUpon 测试用户

在本部分中，我们会在 LearnUpon 中创建一个名为 Britta Simon 的用户。 LearnUpon 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 LearnUpon 中尚不存在用户，身份验证后会创建一个新用户。 如果需要手动创建用户，则需联系 [LearnUpon 支持团队](https://www.learnupon.com/features/support/)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 LearnUpon 磁贴时，应当会自动登录到为其设置了 SSO 的 LearnUpon。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)