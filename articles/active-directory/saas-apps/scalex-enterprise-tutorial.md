---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 ScaleX Enterprise 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ScaleX Enterprise 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e54994d02dd1abbca1602952fbad058b3ad993d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72594265"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>教程：Azure Active Directory 单一登录 (SSO) 与 ScaleX Enterprise 集成

本教程介绍如何将 ScaleX Enterprise 与 Azure Active Directory (Azure AD) 进行集成。 将 ScaleX Enterprise 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 ScaleX Enterprise。
* 让用户使用其 Azure AD 帐户自动登录到 ScaleX Enterprise。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 ScaleX Enterprise 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* ScaleX Enterprise 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-scalex-enterprise-from-the-gallery"></a>从库中添加 ScaleX Enterprise

要配置 ScaleX Enterprise 与 Azure AD 的集成，需要从库中将 ScaleX Enterprise 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“ScaleX Enterprise”   。
1. 从结果面板中选择“ScaleX Enterprise”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>配置并测试 ScaleX Enterprise 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 ScaleX Enterprise 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 ScaleX Enterprise 相关用户之间建立链接关系。

若要配置和测试 ScaleX Enterprise 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 ScaleX Enterprise SSO](#configure-scalex-enterprise-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 ScaleX Enterprise 测试用户](#create-scalex-enterprise-test-user)** - 在 ScaleX Enterprise 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“ScaleX Enterprise”应用程序集成页上，找到“管理”部分，然后选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://platform.rescale.com/saml2/<company id>/`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://platform.rescale.com/saml2/<company id>/acs/`

1. 如果要在 SP  发起的模式下配置应用程序，请点击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [ScaleX Enterprise 客户端支持团队](https://info.rescale.com/contact_sales)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. ScaleX Enterprise 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **emailaddress** 通过 **user.mail** 进行映射。 ScaleX Enterprise 应用程序要求通过 user.userprincipalname 对 emailaddress 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射    。

    ![image](common/edit-attribute.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 ScaleX Enterprise”部分中，根据要求复制相应 URL  。

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

在本部分中，通过授予 B.Simon 访问 ScaleX Enterprise 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“ScaleX Enterprise”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-scalex-enterprise-sso"></a>配置 ScaleX Enterprise SSO

1. 若要在 ScaleX Enterprise 中自动执行配置，需要通过单击“安装扩展”  来安装**我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“设置 ScaleX Enterprise”，此时会将你定向到 ScaleX Enterprise 应用程序  。 在此处，请提供管理员凭据以登录到 ScaleX Enterprise。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 ScaleX Enterprise，请打开新的 Web 浏览器窗口，以管理员身份登录到 ScaleX Enterprise 公司站点，并执行以下步骤：

1. 单击右上角的菜单并选择“Contoso 管理”。 

    > [!NOTE]
    > Contoso 只是一个示例。 这应当是实际公司名称。

    ![配置单一登录](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. 从顶部的菜单中选择“集成”并选择“单一登录”。  

    ![配置单一登录](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. 如下所示填写表单：

    ![配置单一登录](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. 选择“创建可以通过 SSO 进行身份验证的任何用户” 

    b. **服务提供商 saml**：粘贴 urn:oasis:names:tc:SAML:2.0:nameid-format:persistent 值

    c. **ACS 响应中的标识提供者名称电子邮件字段**：粘贴 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 值

    d. **标识提供者 EntityDescriptor 实体 ID：** 粘贴从 Azure 门户复制的“Azure AD 标识符”值  。

    e. **标识提供者 SingleSignOnService URL：** 从 Azure 门户粘贴“登录 URL”。 

    f. **标识提供者公共 X509 证书：** 在记事本中打开从 Azure 下载的 X509 证书并将内容粘贴到此框中。 请确保证书内容的中间没有换行符。

    g. 选中以下复选框：“已启用”、“加密 NameID”和“对 AuthnRequest 进行签名”。 

    h. 单击“更新 SSO 设置”以保存设置。 

### <a name="create-scalex-enterprise-test-user"></a>创建 ScaleX Enterprise 测试用户

为了使 Azure AD 用户能够登录到 ScaleX Enterprise，必须将其预配到 ScaleX Enterprise 中。 对于 ScaleX Enterprise，预配是自动执行的任务，不需要手动执行任何步骤。 在 ScaleX 端会自动预配可以通过 SSO 凭据成功进行身份验证的任何用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 ScaleX Enterprise 磁贴时，应当会自动登录到已为其设置了 SSO 的 ScaleX Enterprise。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 ScaleX Enterprise](https://aad.portal.azure.com/)