---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Jamf Pro 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Jamf Pro 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305433"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Jamf Pro 的集成

本教程介绍如何将 Jamf Pro 与 Azure Active Directory (Azure AD) 集成。 将 Jamf Pro 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Jamf Pro。
* 让用户使用其 Azure AD 帐户自动登录到 Jamf Pro。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Jamf Pro 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Jamf Pro 支持 SP 和 IDP 发起的 SSO 

## <a name="adding-jamf-pro-from-the-gallery"></a>从库添加 Jamf Pro

要配置 Jamf Pro 与 Azure AD 的集成，需要从库中将 Jamf Pro 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Jamf Pro**。 
1. 在结果面板中选择“Jamf Pro”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>配置并测试 Jamf Pro 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Jamf Pro 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Jamf Pro 中的相关用户之间建立链接关系。

若要配置并测试 Jamf Pro 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Jamf Pro SSO](#configure-jamf-pro-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Jamf Pro 测试用户](#create-jamf-pro-test-user)** - 在 Jamf Pro 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Jamf Pro”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.jamfcloud.com/saml/SSO`

1. 如果要在 SP  发起的模式下配置应用程序，请点击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 将从 Jamf Pro 门户中的“单一登录”部分获取实际的标识符值（本教程稍后会介绍）  。 可以从标识符值提取实际的子域值，并在登录 URL 和答复 URL 中使用该子域信息   。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Jamf Pro 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Jamf Pro”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-jamf-pro-sso"></a>配置 Jamf Pro SSO

1. 必须通过单击“安装扩展”  来安装“我的应用安全登录浏览器扩展”  ，才能在 Jamf Pro 中自动执行配置。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“安装 Jamf Pro”  会定向到 Jamf Pro 应用程序。 随后，提供管理员凭据，以登录 Jamf Pro。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Jamf Pro，请打开新的 Web 浏览器窗口，以管理员身份登录到 Jamf Pro 公司站点，并执行以下步骤：

4. 单击页面右上角的“设置”图标  。

    ![Jamf Pro 配置](./media/jamfprosamlconnector-tutorial/configure1.png)

5. 单击“单一登录”  。

    ![Jamf Pro 配置](./media/jamfprosamlconnector-tutorial/configure2.png)

6. 在“单一登录”  页上，执行以下步骤：

    ![Jamf Pro 配置](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. 选中“启用单一登录身份验证”。 

    b. 从“标识提供者”下拉菜单中选择选项“其他”   。

    c. 在“其他提供程序”文本框中输入“Azure AD”   。

    d. 复制“实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。   

    > [!NOTE]
    > 在 `<SUBDOMAIN>` 部分，需要使用此值来填写 Azure 门户上“基本 SAML 配置”部分中的“登录 URL”和“回复 URL”。 

    e. 选择“标识提供者元数据源”下拉菜单中的选项“元数据 URL”，并将你从 Azure 门户复制的“应用联合元数据 URL”值粘贴到下面的文本框中    。

7. 在同一页上，向下滚动到“用户映射”部分并执行以下步骤：  

    ![Jamf Pro 单一登录](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. 为“标识提供者用户映射”选择“NameID”选项。   默认情况下，此设置指定为“NameID”，但可以定义自定义的属性。 

    b. 为“JAMF PRO 用户映射”选择“电子邮件”。   Jamf Pro 按以下方式映射 IdP 发送的 SAML 属性：按用户和按组。 当用户尝试访问 Jamf Pro 时，Jamf Pro 默认会从标识提供者获取有关该用户的信息，并且其与 Jamf Pro 用户帐户相匹配。 如果传入的用户帐户在 Jamf Pro 中不存在，则会发生组名称匹配。

    c. 将值 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 粘贴到“标识提供者组属性名称”文本框中。 

    d. 如果选择“允许用户绕过单一登录身份验证”，则用户将不会重定向到标识提供者登录页进行身份验证，而可以直接登录到 Jamf Pro。  当用户尝试通过标识提供者访问 Jamf Pro 时，会发生 IdP 发起的 SSO 身份验证和授权。

    e. 单击“ **保存**”。

### <a name="create-jamf-pro-test-user"></a>创建 Jamf Pro 测试用户

要使 Azure AD 用户能够登录到 Jamf Pro，必须将其预配到 Jamf Pro 中。 就 Jamf Pro 来说，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Jamf Pro 公司站点。

2. 单击页面右上角的“设置”图标  。

    ![添加员工](./media/jamfprosamlconnector-tutorial/configure1.png)

3. 单击“Jamf Pro 用户帐户和组”  。

    ![添加员工](./media/jamfprosamlconnector-tutorial/user1.png)

4. 单击“新建”  。

    ![添加员工](./media/jamfprosamlconnector-tutorial/user2.png)

5. 选择“创建标准帐户”  。

    ![添加员工](./media/jamfprosamlconnector-tutorial/user3.png)

6. 在“新建帐户”对话框执行以下步骤  ：

    ![添加员工](./media/jamfprosamlconnector-tutorial/user4.png)

    a. 在“用户名”文本框中键入“BrittaSimon”的全名  。

    b. 根据你的组织为“访问级别”、“特权设置”和“访问状态”选择合适的选项    。

    c. 在“全名”文本框中，键入 Britta Simon 的全名  。

    d. 在“电子邮件地址”文本框中，键入 Britta Simon 帐户的电子邮件地址  。

    e. 在“密码”文本框中，键入用户的密码  。

    f. 在“验证密码”文本框中，键入用户的密码  。

    g. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Jamf Pro 磁贴时，应会自动登录到为其设置了 SSO 的 Jamf Pro。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Jamf Pro](https://aad.portal.azure.com/)

