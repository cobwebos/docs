---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 ThousandEyes 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ThousandEyes 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373238"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>教程：Azure Active Directory 单一登录 (SSO) 与 ThousandEyes 集成

本教程介绍如何将 ThousandEyes 与 Azure Active Directory (Azure AD) 集成。 将 ThousandEyes 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 ThousandEyes。
* 让用户使用其 Azure AD 帐户自动登录到 ThousandEyes。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 ThousandEyes 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* ThousandEyes 支持 **SP 和 IDP** 发起的 SSO
* ThousandEyes 支持[**自动**用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-thousandeyes-from-the-gallery"></a>从库添加 ThousandEyes

要配置 ThousandEyes 与 Azure AD 的集成，需从库中将 ThousandEyes 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“ThousandEyes”   。
1. 从结果面板中选择“ThousandEyes”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>配置并测试 ThousandEyes 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 ThousandEyes 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 ThousandEyes 相关用户之间建立链接关系。

若要配置和测试 ThousandEyes 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 ThousandEyes SSO](#configure-thousandeyes-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 ThousandEyes 测试用户](#create-thousandeyes-test-user)** - 在 ThousandEyes 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“ThousandEyes”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置  部分，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”  按钮来保存配置。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.thousandeyes.com/login/sso` 

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 ThousandEyes”部分中，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分中，通过授予 B.Simon 访问 ThousandEyes 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“ThousandEyes”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-thousandeyes-sso"></a>配置 ThousandEyes SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 **ThousandEyes** 公司站点。

2. 在顶部菜单中，单击“设置”。 

    ![设置](./media/thousandeyes-tutorial/ic790066.png "设置")

3. 单击“帐户” 

    ![帐户](./media/thousandeyes-tutorial/ic790067.png "Account")

4. 单击“安全性和身份验证”  选项卡。

    ![安全性和身份验证](./media/thousandeyes-tutorial/ic790068.png "安全性和身份验证")

5. 在“设置单一登录”  部分中，执行以下步骤：

    ![设置单一登录](./media/thousandeyes-tutorial/ic790069.png "设置单一登录")

    a. 选择“启用单一登录”  。

    b. 在“登录页 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。

    c. 在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。

    d. 在“标识提供者颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”   。

    e. 在“验证证书”中，单击“选择文件”，然后上传从 Azure 门户下载的证书   。

    f. 单击“ **保存**”。

### <a name="create-thousandeyes-test-user"></a>创建 ThousandEyes 测试用户

本部分的目的是在 ThousandEyes 中创建名为“Britta Simon”的用户。 ThousandEyes 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](thousandeyes-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤： 

1. 以管理员身份登录到 ThousandEyes 公司站点。

2. 单击“设置”  。

    ![设置](./media/thousandeyes-tutorial/IC790066.png "设置")

3. 单击“帐户”  。

    ![帐户](./media/thousandeyes-tutorial/IC790067.png "Account")

4. 单击“帐户和用户”  选项卡。

    ![帐户和用户](./media/thousandeyes-tutorial/IC790073.png "帐户和用户")

5. 在“添加用户和帐户”  部分中，执行以下步骤：

    ![添加用户帐户](./media/thousandeyes-tutorial/IC790074.png "添加用户帐户")

    a. 在“全名”文本框中，键入用户的名称（例如 **B.Simon**）。 

    b. 在“电子邮件”文本框中，键入用户的电子邮件，例如 b.simon@contoso.com。 

    b. 单击“将新用户添加到帐户”  。

    > [!NOTE]
    > Azure Active Directory 帐户持有者会收到一封电子邮件，其中包含用于确认和激活帐户的链接。

> [!NOTE]
> 可使用任何其他 ThousandEyes 用户帐户创建工具或使用 ThousandEyes 提供的 API 来预配 Azure Active Directory 用户帐户。


## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“ThousandEyes”磁贴时，应会自动登录到设置了 SSO 的 ThousandEyes。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 ThousandEyes](https://aad.portal.azure.com/)

- [配置用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)