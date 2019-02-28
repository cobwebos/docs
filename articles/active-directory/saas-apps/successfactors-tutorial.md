---
title: 教程：Azure Active Directory 与 SuccessFactors 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SuccessFactors 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c964d4e070bafc6e8e66535c3963d82803d3cd1e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865767"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>教程：Azure Active Directory 与 SuccessFactors 集成

在本教程中，了解如何将 SuccessFactors 与 Azure Active Directory (Azure AD) 集成。
将 SuccessFactors 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SuccessFactors。
* 可让用户使用其 Azure AD 帐户自动登录到 SuccessFactors（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SuccessFactors 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 SuccessFactors 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SuccessFactors 支持 **SP** 发起的 SSO

## <a name="adding-successfactors-from-the-gallery"></a>从库添加 SuccessFactors

要配置 SuccessFactors 与 Azure AD 的集成，需要从库中将 SuccessFactors 添加到托管 SaaS 应用列表。

**若要从库中添加 SuccessFactors，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“SuccessFactors”，在结果面板中选择“SuccessFactors”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 SuccessFactors](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 SuccessFactors 的 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 SuccessFactors 中的相关用户之间建立链接关系。

若要配置和测试 SuccessFactors 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SuccessFactors 单一登录](#configure-successfactors-single-sign-on)** - 在应用程序端配置“单一登录”设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SuccessFactors 测试用户](#create-successfactors-test-user)** - 在 SuccessFactors 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SuccessFactors 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 **SuccessFactors** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![SuccessFactors 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. 在“标识符”文本框中，使用以下模式键入 URL：
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

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：
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

5. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 SuccessFactors”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-successfactors-single-sign-on"></a>配置 SuccessFactors 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 **SuccessFactors 管理门户**。

2. 请访问“应用程序安全性”并从本机转到“单一登录功能”。

3. 将任何值放在“重置令牌”中，然后单击“保存令牌”以启用 SAML SSO。

    ![在应用端配置单一登录][11]

    > [!NOTE]
    > 此值将用作打开/关闭开关。 如果保存了任何值，则 SAML SSO 为打开。 如果保存为空值，则 SAML SSO 为关闭。

4. 从本机转到以下屏幕快照并执行以下操作：

    ![在应用端配置单一登录][12]
  
    a. 选择“SAML v2 SSO”单选按钮
  
    b. 设置“SAML 声明方名称”（例如，SAML 颁发者+公司名称）。

    c. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    d. 对于“需要强制签名”，选择“断言”。

    e. 对于“启用 SAML 标志”选择“启用”。

    f. 对于“登录请求签名（SF 生成/SP/RP）”选择“否”。

    g. 对于“SAML 配置文件”，选择“浏览器/Post 配置文件”。

    h. 对于“强制证书有效期限”，选择“否”。

    i. 复制从 Azure 门户下载的证书文件的内容，然后将其粘贴到“SAML 验证证书”文本框。

    > [!NOTE] 
    > 证书内容必须具有开始证书和结束证书标记。

5. 导航到 SAML V2，并执行以下步骤：

    ![在应用端配置单一登录][13]

    a. 对于“支持 SP 启动的全局注销”，选择“是”。

    b. 在“全局注销服务URL (LogoutRequest 目标)”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    c. 对于“要求 sp 必须加密所有 NameID 元素”选择“否”。

    d. 对于“NameID 格式”，选择“未指定”。

    e. 对于“启用 sp 启动的登录 (AuthnRequest)”选择“是”。

    f. 在“以公司范围颁发者身份发送请求”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

6. 如果希望登录用户名不区分大小写，请执行这些步骤。

    ![配置单一登录][29]

    a. 请访问“公司设置”（靠近底部）。

    b. 选中“启用不区分大小写的用户名”旁边的复选框。

    c. 单击“ **保存**”。

    > [!NOTE]
    > 如果尝试启用此功能，系统会检查是否会创建重复的 SAML 登录名。 例如，如果客户具有用户名 User1 和 user1。 取消区分大小写会认定它们重复。 系统将提供一条错误消息，且不会启用该功能。 客户需要更改其中一个用户名，以使其拼写不同。

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

在本部分中，通过授予 Britta Simon 访问 SuccessFactors 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SuccessFactors”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SuccessFactors”。

    ![应用程序列表中的 SuccessFactors 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-successfactors-test-user"></a>创建 SuccessFactors 测试用户

要使 Azure AD 用户能够登录 SuccessFactors，必须将这些用户预配到 SuccessFactors 中。 对于 SuccessFactors，预配任务需要手动完成。

若要在 SuccessFactors 中创建用户，需要联系 [SuccessFactors 支持团队](https://www.successfactors.com/content/ssf-site/en/support.html)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Salesforce 磁贴时，应当会自动登录到已设置了 SSO 的 SuccessFactors。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
