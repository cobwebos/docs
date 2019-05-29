---
title: 教程：Azure Active Directory 与 Leapsome 集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 和 Leapsome 之间的单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: dbdf6847658e6a5a3999007f51b409e47a65e4cd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988079"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>教程：Azure Active Directory 与 Leapsome 集成

本教程介绍了如何将 Leapsome 与 Azure Active Directory (Azure AD) 集成。
将 Leapsome 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Leapsome。
* 可让用户使用其 Azure AD 帐户自动登录到 Leapsome（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置将 Azure AD 与 Leapsome 集成，需要有以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了 Leapsome 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Leapsome 支持 SP 和 IDP 发起的 SSO 

## <a name="adding-leapsome-from-the-gallery"></a>从库中添加 Leapsome

若要配置将 Leapsome 集成到 Azure AD，需要将 Leapsome 从库中添加到托管 SaaS 应用程序列表。

**若要从库中添加 Leapsome，请按照以下步骤操作：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Leapsome”  ，从结果面板中选择“Leapsome”  ，再单击“添加”  按钮，以添加应用程序。

    ![结果列表中的“Leapsome”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分以测试用户“Britta Simon”为例，介绍了如何配置和测试 Azure AD 与 Leapsome 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Leapsome 相关用户之间建立链接关系。

若要配置和测试 Azure AD 与 Leapsome 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Leapsome 单一登录](#configure-leapsome-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Leapsome 测试用户](#create-leapsome-test-user)** - 在 Leapsome 中创建与 Britta Simon 对等的用户，并将它与用户的 Azure AD 身份关联。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Azure AD 与 Leapsome 单一登录，请按照以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Leapsome 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    ![[应用程序名称] 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入 URL：`https://www.leapsome.com` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![[应用程序名称] 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > 上面的回复 URL 和登录 URL 不是实际值。 将使用实际值更新这些值（本教程稍后将会介绍）。

6. Leapsome 应用程序需要特定格式的 SAML 断言，这要求向“SAML 令牌属性”配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。  

    ![image](common/edit-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ： 

    | 名称 | 源属性 | 命名空间 |
    | ---------------| --------------- | --------- |  
    | 名 | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | 姓 | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | 员工图片的 URL | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > 图片属性值不是实际值。 请使用实际图片 URL 更新此值。 若要获取此值，请联系 [Leapsome 客户端支持团队](mailto:support@leapsome.com)。

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 在“命名空间”  文本框中，键入相应行的命名空间 URI。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

9. 在“设置 Leapsome”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-leapsome-single-sign-on"></a>Leapsome 单一登录配置

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Leapsome。

1. 依次单击右上角的“设置”徽标和“管理员设置”  。

    ![Leapsome 设置](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. 单击左侧菜单栏上的“单一登录(SSO)”  ，并在“基于 SAML 的单一登录(SSO)”  页上按照以下步骤操作：

    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. 选中“启用基于 SAML 的单一登录”  。

    b. 将“登录 URL (指引用户开始登录)”  值复制并粘贴到 Azure 门户上“基本 SAML 配置”  部分中的“登录 URL”  文本框。

    c. 将“回复 URL (接收标识提供程序的响应)”值复制并粘贴到 Azure 门户上“基本 SAML 配置”部分中的“回复 URL”文本框    。

    d. 在“SSO 登录 URL (由标识提供程序提供)”  文本框中，粘贴从 Azure 门户复制的“登录 URL”  值。

    e. 将从 Azure 门户下载的证书（不含 `--BEGIN CERTIFICATE and END CERTIFICATE--` 注释）复制并粘贴到“证书(由标识提供程序提供)”  文本框。

    f. 单击“更新 SSO 设置”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 `brittasimon@yourcompanydomain.extension`   
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分介绍了如何向 Britta Simon 授予对 Leapsome 访问权限，让其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Leapsome”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Leapsome”  。

    ![应用程序列表中的“Leapsome”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-leapsome-test-user"></a>创建 Leapsome 测试用户

本部分介绍了如何在 Leapsome 中创建用户“Britta Simon”。 请与 [Leapsome 客户端支持团队](mailto:support@leapsome.com)协作，将必须添加到允许列表的用户或域添加到 Leapsome 平台中。 如果域是由团队添加，用户会自动预配到 Leapsome 平台。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Leapsome 磁贴时，应当会自动登录到为其设置了 SSO 的 Leapsome。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)