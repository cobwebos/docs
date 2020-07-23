---
title: 教程：Azure Active Directory 与 LockPath Keylight 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 LockPath Keylight 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148c2c46a911088d01ab83fe2d16e8ca81d272ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098779"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>教程：Azure Active Directory 与 LockPath Keylight 集成

本教程介绍如何将 LockPath Keylight 与 Azure Active Directory (Azure AD) 集成。
将 LockPath Keylight 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 LockPath Keylight。
* 可让用户使用其 Azure AD 帐户自动登录到 LockPath Keylight（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 LockPath Keylight 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 LockPath Keylight 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* LockPath Keylight 支持 SP 发起的 SSO 
* LockPath Keylight 支持实时用户预配 

## <a name="adding-lockpath-keylight-from-the-gallery"></a>从库中添加 LockPath Keylight

要配置 LockPath Keylight 与 Azure AD 的集成，需要从库中将 LockPath Keylight 添加到托管 SaaS 应用列表。

若要从库中添加 LockPath Keylight，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“LockPath Keylight”，在结果面板中选择“LockPath Keylight”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 LockPath Keylight](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 LockPath Keylight 配置和测试 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 LockPath Keylight 中的相关用户之间建立链接关系。

若要使用 LockPath Keylight 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 LockPath Keylight 单一登录](#configure-lockpath-keylight-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 LockPath Keylight 测试用户](#create-lockpath-keylight-test-user)** - 在 LockPath Keylight 中创建 Britta Simon 的对应用户，并将它与用户的 Azure AD 身份关联。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要使用 LockPath Keylight 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“LockPath Keylight”应用程序集成页上，单选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![LockPath Keylight 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.keylightgrc.com/`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<company name>.keylightgrc.com`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [LockPath Keylight 客户端支持团队](https://www.lockpath.com/contact/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求通过从给定的选项下载**证书(原始)** 并将其保存在计算机上。

    ![证书下载链接](common/certificateraw.png)

6. 在“设置 LockPath Keylight”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>配置 LockPath Keylight 单一登录

1. 若要在 LockPath Keylight 中启用 SSO，请执行以下步骤：

    a. 以管理员身份登录到 LockPath Keylight 帐户。

    b. 在顶部菜单中，单击“人员”  ，并选择“Keylight 设置”  。

    ![配置单一登录](./media/keylight-tutorial/401.png)

    c. 在左侧树视图中，单击“SAML”  。

    ![配置单一登录](./media/keylight-tutorial/402.png)

    d. 在“SAML 设置”  对话框中，单击“编辑”  。

    ![配置单一登录](./media/keylight-tutorial/404.png)

1. 在“编辑 SAML 设置”  对话框页上，执行以下步骤：

    ![配置单一登录](./media/keylight-tutorial/405.png)

    a. 将“SAML 身份验证”设置为“活动”   。

    b. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    d. 单击“选择文件”  ，选择已下载的 LockPath Keylight 证书，并单击“打开”  上传证书。

    e. 将“SAML 用户 ID 位置”设置为 subject 语句的 NameIdentifier 元素   。

    f. 使用以下模式提供“Keylight 服务提供商”  ：`https://<CompanyName>.keylightgrc.com`。

    g. 将“自动预配用户”  设置为“活动”  。

    h. 将“自动预配帐户类型”  设置为“全部用户”  。

    i. 设置“自动预配安全角色”  ，选择“使用 SAML 的标准用户”  。

    j. 设置“自动预配安全配置”  ，选择“标准用户配置”  。

    k. 在“电子邮件属性”  文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    l. 在“名字属性”  文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。

    m. 在“姓氏属性”  文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。

    n. 单击“保存”  。

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

在本部分中，通过授予 Britta Simon 访问 LockPath Keylight 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“LockPath Keylight”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“LockPath Keylight”  。

    ![应用程序列表中的 LockPath Keylight 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-lockpath-keylight-test-user"></a>创建 LockPath Keylight 测试用户

本部分将在 LockPath Keylight 中创建一个名为“Britta Simon”的用户。 LockPath Keylight 支持在默认情况下启用的实时用户预配。 此部分不存在任何操作项。 如果 LockPath Keylight 中尚不存在用户，身份验证后会创建一个新用户。 如果需要手动创建用户，则需要联系 [LockPath Keylight 客户端支持团队](https://www.lockpath.com/contact/)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 LockPath Keylight 磁贴时，应会自动登录到为其设置了 SSO 的 LockPath Keylight。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)