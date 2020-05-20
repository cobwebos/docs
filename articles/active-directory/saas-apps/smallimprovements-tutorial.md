---
title: 教程：Azure Active Directory 与 Small Improvements 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Small Improvements 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090360"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>教程：Azure Active Directory 与 Small Improvements 集成

本教程介绍如何将 Small Improvements 与 Azure Active Directory (Azure AD) 集成。
将 Small Improvements 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Small Improvements。
* 可以让用户使用其 Azure AD 帐户自动登录到 Small Improvements（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Small Improvements 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Small Improvements 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Small Improvements 支持 SP 发起的 SSO 

## <a name="adding-small-improvements-from-the-gallery"></a>从库中添加 Small Improvements

要配置 Small Improvements 与 Azure AD 的集成，需要从库中将 Small Improvements 添加到托管 SaaS 应用列表。

**若要从库中添加 Small Improvements，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Small Improvements”，在结果面板中选择“Small Improvements”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Small Improvements](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 Small Improvements 的 Azure AD 单一登录  。
要使单一登录有效，需要在 Azure AD 用户与 Small Improvements 相关用户之间建立链接关系。

若要配置和测试 Small Improvements 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **配置 Small Improvements 单一登录[ - 在应用程序端配置单一登录设置](#configure-small-improvements-single-sign-on)** 。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **创建 Small Improvements 测试用户[ - 在 Small Improvements 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式](#create-small-improvements-test-user)** 。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要配置 Small Improvements 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 Small Improvements 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Small Improvements 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.small-improvements.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Small Improvements 客户端支持团队](mailto:support@small-improvements.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Small Improvements”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-small-improvements-single-sign-on"></a>配置 Small Improvements 单一登录

1. 在另一浏览器窗口中，以管理员身份登录 Small Improvements 公司站点。

1. 在主仪表板页中，单击左侧的“管理”  按钮。

    ![配置单一登录](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. 在“集成”部分中，单击“SAML SSO”按钮。

    ![配置单一登录](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. 在“SSO 设置”页上，执行以下步骤：

    ![配置单一登录](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. 在“HTTP 终结点”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在记事本中打开下载的证书，复制其内容，并将其粘贴到“x509 证书”文本框中。  

    c. 如果想要让 SSO 和登录表单身份验证选项可供用户使用，请选中“也启用通过登录名/密码访问”  选项。  

    d. 在“SAML 提示”  文本框中输入用于命名“SSO 登录”按钮的相应值。  

    e. 单击“保存”  。

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

在本部分中，将通过授予 Britta Simon 访问 Small Improvements 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Small Improvements”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Small Improvements”  。

    ![应用程序列表中的 Small Improvements 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-small-improvements-test-user"></a>创建 Small Improvements 测试用户

为使 Azure AD 用户可登录 Small Improvements，必须将用户预配到 Small Improvements 中。 使用 Small Improvements 时，需手动预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Small Improvements 公司站点。

1. 从主页转到左侧菜单，单击“管理”  。

1. 从“用户管理”部分单击“用户目录”  按钮。

    ![创建 Azure AD 测试用户](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. 单击“添加用户”  。

    ![创建 Azure AD 测试用户](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. 在“添加用户”对话框中，执行以下步骤  ： 

    ![创建 Azure AD 测试用户](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. 输入用户的名字，如 Britta   。

    b. 输入用户的姓氏，如 Simon   。

    c. 输入用户的“电子邮件地址”，如 brittasimon@contoso.com。

    d. 还可以选择在“发送通知电子邮件”  框中输入个人消息。 如果不希望发送通知，请取消选中此复选框。

    e. 单击“创建用户”  。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Small Improvements 磁贴时，应当会自动登录到为其设置了 SSO 的 Small Improvements。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)