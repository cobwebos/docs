---
title: 教程：Azure Active Directory 与 Everbridge 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Everbridge 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231463"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>教程：Azure Active Directory 与 Everbridge 集成

本教程介绍了如何将 Everbridge 与 Azure Active Directory (Azure AD) 集成。
将 Everbridge 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Everbridge。
* 让用户可使用其 Azure AD 帐户自动登录到 Everbridge。 这种访问控制称为单一登录 (SSO)。
* 使用 Azure 门户在一个中心位置管理帐户。
有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Everbridge 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 使用单一登录的 Everbridge 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Everbridge 支持 IDP 发起的 SSO。

## <a name="add-everbridge-from-the-azure-marketplace"></a>通过 Azure 市场添加 Everbridge

要配置 Everbridge 与 Azure AD 的集成，请将 Azure 市场中的 Everbridge 添加到托管 SaaS 应用列表。

要添加 Azure 市场中的 Everbridge，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)中，在左侧导航窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新建应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入 Everbridge。 选择结果面板中的 Everbridge，然后选择“添加”。

     ![结果列表中的 Everbridge](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 Britta Simon 的测试用户配置和测试 Everbridge 的 Azure AD 单一登录。
若要正常使用单一登录，请在 Azure AD 用户与 Everbridge 中的相关用户之间建立链接关系。

若要配置和测试 Everbridge 的 Azure AD 单一登录，完成以下构建基块：

- [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
- [将 Everbridge 配置为 Everbridge 管理员门户单一登录](#configure-everbridge-as-everbridge-manager-portal-single-sign-on)，从而在应用程序端配置单一登录设置。
- [将 Everbridge 配置为 Everbridge 成员门户单一登录](#configure-everbridge-as-everbridge-member-portal-single-sign-on)，从而在应用程序端配置单一登录设置。
- [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
- [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
- [创建 Everbridge 测试用户](#create-an-everbridge-test-user)，从而在 Everbridge 中创建 Britta Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
- [测试单一登录](#test-single-sign-on)，以验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Everbridge 的 Azure AD 单一登录，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com/)中的 Everbridge 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”来打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

    >[!NOTE]
    >将应用程序配置为 Azure 门户和 Everbridge 门户的管理员门户或成员门户。

4. 若要将 Everbridge 应用程序配置为 Everbridge 管理员门户，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Everbridge 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”框中，输入采用以下模式的 URL：`https://sso.everbridge.net/<API_Name>`

    b. 在“回复 URL”框中，输入采用以下模式的 URL：`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符和回复 URL 将其更新。 请联系 [Everbridge 支持团队](mailto:support@everbridge.com)获取。 还可参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 若要将 Everbridge 应用程序配置为 Everbridge 成员门户，请在“基本 SAML 配置”部分中执行以下步骤：

  * 如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

     ![IDP 发起模式的 Everbridge 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”框中，输入采用以下模式的 URL：`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. 在“回复 URL”框中，输入采用以下模式的 URL：`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”并执行以下步骤：

     ![SP 发起模式的 Everbridge 域和 URL 单一登录信息](common/both-signonurl.png)

     a. 在“登录 URL”框中，输入采用以下模式的 URL：`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > 这些不是实际值。 使用实际标识符、回复 URL 和登录 URL 将其更新。 请联系 [Everbridge 支持团队](mailto:support@everbridge.com)获取。 还可参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

6. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，选择“下载”以下载“联合元数据 XML”。 然后将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Everbridge”部分，根据要求复制所需的 URL：

    ![复制配置 URL](common/copy-configuration-urls.png)

    - 登录 URL
    - Azure AD 标识符
    - 注销 URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>将 Everbridge 配置为 Everbridge 管理员门户单一登录

若要将 Everbridge 上的 SSO 配置为“Everbridge 管理员门户”应用程序，请执行以下步骤。
 
1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Everbridge。

1. 在顶部菜单中，选择“设置”选项卡。在“安全性”下，选择“单一登录”。
   
     ![配置单一登录](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. 在“名称”框中，输入标识符提供者的名称。 例如，贵公司的名称。
   
     b. 在“API 名称”框中，输入 API 的名称。
   
     c. 选择“选择文件”，上传从 Azure 门户下载的元数据文件。
   
     d. 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”。
   
     e. 在“标识提供者登录 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值。
   
     f. 对于“服务提供程序发起的请求绑定”，选择“HTTP 重定向”。

     g. 选择“保存”。

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>将 Everbridge 配置为 Everbridge 成员门户单一登录

若要将 Everbridge 的单一登录配置为 Everbridge 成员门户，请将下载的“联合元数据 XML”发送给 [Everbridge 支持团队](mailto:support@everbridge.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

若要在 Azure 门户中创建名为 Britta Simon 的测试用户，请执行以下操作。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。

    ![“用户”和“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户”对话框中执行以下步骤。

    ![“用户”对话框](common/user-properties.png)

    a. 在“姓名”框中，输入 **BrittaSimon**。
  
    b. 在“用户名”框中输入 `brittasimon@yourcompanydomain.extension`。 例如 BrittaSimon@contoso.com。

    c. 选中“显示密码”复选框。 记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

通过向 Britta Simon 授予访问 Everbridge 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” >“Everbridge”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Everbridge”。

    ![应用程序列表中的 Everbridge 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”。 在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”对话框](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表内的“Britta Simon”。 选择屏幕底部的“选择”。

6. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 选择屏幕底部的“选择”。

7. 在“添加分配”对话框中选择“分配”。

### <a name="create-an-everbridge-test-user"></a>创建 Everbridge 测试用户

在本部分中，会在 Everbridge 中创建测试用户 Britta Simon。 要将用户添加到 Everbridge 平台，请与 [Everbridge 支持团队](mailto:support@everbridge.com)协作。 使用单一登录前，必须先在 Everbridge 中创建并激活用户。 

### <a name="test-single-sign-on"></a>测试单一登录 

使用访问面板测试 Azure AD 单一登录配置。

如果在访问面板中选择 Everbridge 磁贴，应会自动登录到设置了 SSO 的 Everbridge 帐户。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

