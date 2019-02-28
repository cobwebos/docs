---
title: 教程：Azure Active Directory 与 Comm100 Live Chat 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Comm100 Live Chat 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 873b9d89889317a5c406313811c74286caf7d0e1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882036"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>教程：Azure Active Directory 与 Comm100 Live Chat 的集成

本教程介绍如何将 Comm100 Live Chat 与 Azure Active Directory (Azure AD) 集成。
将 Comm100 Live Chat 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Comm100 Live Chat。
* 可以让用户使用其 Azure AD 帐户自动登录到 Comm100 Live Chat（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Comm100 Live Chat 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Comm100 Live Chat 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Comm100 Live Chat 支持 **SP** 发起的 SSO

## <a name="adding-comm100-live-chat-from-the-gallery"></a>从库中添加 Comm100 Live Chat

要配置 Comm100 Live Chat 与 Azure AD 的集成，需要从库中将 Comm100 Live Chat 添加到托管 SaaS 应用列表。

**若要从库中添加 Comm100 Live Chat，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Comm100 Live Chat”，在结果面板中选择“Comm100 Live Chat”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Comm100 Live Chat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Comm100 Live Chat 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Comm100 Live Chat 相关用户之间建立链接关系。

若要配置和测试 Comm100 Live Chat 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Comm100 Live Chat 单一登录](#configure-comm100-live-chat-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Comm100 Live Chat 测试用户](#create-comm100-live-chat-test-user)** - 在 Comm100 Live Chat 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Comm100 Live Chat 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Comm100 Live Chat”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Comm100 Live Chat 域和 URL 单一登录信息](common/sp-signonurl.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > 登录 URL 值不是实际值。 需使用实际登录 URL 更新“登录 URL”值（本教程稍后将会介绍）。

5. Comm100 Live Chat 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤： 

    | Name |  源属性|
    | ---------------| --------------- |
    |   电子邮件    | user.mail |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

4. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Comm100 Live Chat”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-comm100-live-chat-single-sign-on"></a>配置 Comm100 Live Chat 单一登录

9. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Comm100 Live Chat。

10. 单击页面右上角的“我的帐户”。

    ![Comm100 Live Chat - 我的帐户](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. 在菜单左侧单击“安全性”，然后单击“代理单一登录”。

    ![Comm100 Live Chat - 安全性](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. 在“代理单一登录”页上，执行以下步骤：

    ![Comm100 Live Chat - 安全性](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. 复制第一个突出显示的链接，并将其粘贴到 Azure 门户上“Comm100 Live Chat 域和 URL”部分中的“单一登录 URL”文本框内。

    b. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 单击“选择文件”，将从 Azure 门户下载的 base-64 编码证书上传到“证书”中。

    e. 单击“保存更改”

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Comm100 Live Chat 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Comm100 Live Chat”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Comm100 Live Chat”。

    ![应用程序列表中的 Comm100 Live Chat 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-comm100-live-chat-test-user"></a>创建 Comm100 Live Chat 测试用户

为了使 Azure AD 用户能够登录 Comm100 Live Chat，必须将其预配到 Comm100 Live Chat 中。 在 Comm100 Live Chat 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Comm100 Live Chat。

2. 单击页面右上角的“我的帐户”。

    ![Comm100 Live Chat - 我的帐户](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. 从菜单左侧单击“代理”，然后单击“新建代理”。

    ![Comm100 Live Chat - 代理](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. 在“新建代理”页上执行以下步骤：

    ![Comm100 Live Chat - 新建代理](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. 在“电子邮件”文本框中，输入用户的电子邮件，例如 **Brittasimon@contoso.com**。

    b. 在“名字”文本框中，输入用户的名字，如 Britta。

    c. 在“姓氏”文本框中，输入用户的姓氏，例如 **simon**。

    d. 在“显示名称”文本框中，输入用户的显示名称，例如 **Britta Simon**。

    e. 在“密码”文本框中键入密码。

    f. 单击“ **保存**”。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Comm100 Live Chat 磁贴时，应当会自动登录到你为其设置了 SSO 的 Comm100 Live Chat。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

