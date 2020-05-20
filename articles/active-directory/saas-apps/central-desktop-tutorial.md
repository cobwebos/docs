---
title: 教程：Azure Active Directory 与 Central Desktop 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Central Desktop 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda8e928b530001faeae34c364dfed91d7620f0a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157510"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>教程：Azure Active Directory 与 Central Desktop 集成

本教程介绍了如何将 Central Desktop 与 Azure Active Directory (Azure AD) 集成。
将 Central Desktop 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Central Desktop。
* 可以让用户使用其 Azure AD 帐户自动登录到 Central Desktop（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Central Desktop 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Central Desktop 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Central Desktop 支持 **SP** 发起的 SSO

## <a name="adding-central-desktop-from-the-gallery"></a>从库中添加 Central Desktop

要配置 Central Desktop 与 Azure AD 的集成，需要从库中将 Central Desktop 添加到托管 SaaS 应用列表。

**若要从库中添加 Central Desktop，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Central Desktop”，在结果面板中选择“Central Desktop”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Central Desktop](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为“Britta Simon”的测试用户配置并测试 Central Desktop 的 Azure AD 单一登录。 
若要运行单一登录，需要在 Azure AD 用户与 Central Desktop 相关用户之间建立链接关系。

要配置和测试 Central Desktop 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Central Desktop 单一登录](#configure-central-desktop-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Central Desktop 测试用户](#create-central-desktop-test-user)** - 在 Central Desktop 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要为 Central Desktop 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Central Desktop”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Central Desktop 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<companyname>.centraldesktop.com`。

    b. 在“标识符”框中，使用以下模式键入 URL： 
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Central Desktop 客户端支持团队](https://imeetcentral.com/contact-us)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求通过从给定的选项下载**证书(原始)** 并将其保存在计算机上。

    ![证书下载链接](common/certificateraw.png)

6. 在“设置 Central Desktop”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-central-desktop-single-sign-on"></a>配置 Central Desktop 单一登录

1. 登录到 Central Desktop  租户。

2. 转到“设置”  。 选择“高级”  ，然后选择“单一登录”  。

    ![设置 - 高级](./media/central-desktop-tutorial/ic769563.png "设置 - 高级")

3. 在“单一登录设置”  页上，执行以下步骤：

    ![单一登录设置](./media/central-desktop-tutorial/ic769564.png "单一登录设置")

    a. 选择“启用 SAML v2 单一登录”  。

    b. 在“SSO URL”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“SSO 登录 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    d. 在“SSO 注销 URL”框中，粘贴从 Azure 门户复制的“注销 URL”值   。

4. 在“消息签名验证方法”部分执行以下步骤  ：

    ![消息签名验证方法](./media/central-desktop-tutorial/ic769565.png "消息签名验证方法")
    
    a. 选择“证书”。 

    b. 从“SSO 证书”列表中，选择“RSH SHA256”。

    c. 在记事本中打开已下载的证书。 然后将证书内容复制并粘贴到“SSO 证书”  字段。

    d. 选择“显示 SAMLv2 登录页的链接”。 

    e. 选择“更新”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 Central Desktop 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”  、“所有应用程序”  和“Central Desktop”  。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Central Desktop”  。

    ![应用程序列表中的 Central Desktop 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-central-desktop-test-user"></a>创建 Central Desktop 测试用户

要使 Azure AD 用户能够登录，必须将这些用户预配到 Central Desktop 应用程序中。 本部分介绍如何在 Central Desktop 中创建 Azure AD 用户帐户。

> [!NOTE]
> 可以使用 Central Desktop 提供的任何其他 Central Desktop 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

**为 Central Desktop 预配用户帐户：**

1. 登录到 Central Desktop 租户。

2. 选择“人员”，然后选择“添加内部成员”   。

    ![People](./media/central-desktop-tutorial/ic781051.png "人员")

3. 在“新成员的电子邮件地址”框中，键入要预配的 Azure AD 帐户，并选择“下一步”   。

    ![新成员的电子邮件地址](./media/central-desktop-tutorial/ic781052.png "新成员的电子邮件地址")

4. 选择“添加内部成员”  。

    ![添加内部成员](./media/central-desktop-tutorial/ic781053.png "添加内部成员")
  
   > [!NOTE]
   > 所添加的用户会收到一封电子邮件，其中包含用于激活其帐户的确认链接。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Central Desktop 磁贴时，应当会自动登录到你为其设置了 SSO 的 Central Desktop。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
