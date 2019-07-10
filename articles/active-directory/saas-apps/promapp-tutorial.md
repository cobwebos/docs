---
title: 教程：Azure Active Directory 与 Promapp 集成 | Microsoft Docs
description: 本教程介绍如何在 Azure Active Directory 与 Promapp 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 2ddb8777a6470c0e739545e71867a694022d1723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093606"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>教程：Azure Active Directory 与 Promapp 的集成

本教程介绍如何将 Promapp 与 Azure Active Directory (Azure AD) 集成。
这种集成可提供以下优势：

* 可使用 Azure AD 控制谁有权访问 Promapp。
* 可让用户使用其 Azure AD 帐户自动登录到 Promapp（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Promapp 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以注册[一个月试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 启用了单一登录的 Promapp 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

* Promapp 支持 SP 和 IdP 发起的 SSO。

* Promapp 支持恰时用户预配。

## <a name="add-promapp-from-the-gallery"></a>从库中添加 Promapp

要设置 Promapp 与 Azure AD 的集成，需要从库中将 Promapp 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”： 

    ![选择“Azure Active Directory”](common/select-azuread.png)

2. 转到“企业应用程序” > “所有应用程序”   ：

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加应用程序，请在窗口顶部选择“新建应用程序”  ：

    ![选择“新建应用程序”](common/add-new-app.png)

4. 在搜索框中，输入“Promapp”  。 在搜索结果中选择“Promapp”，然后选择“添加”。  

     ![搜索结果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将使用名为 Britta Simon 的测试用户来配置并测试 Promapp 的 Azure AD 单一登录。
若要启用单一登录，需要在 Azure AD 用户与 Promapp 中的对应用户之间建立关系。

若要配置并测试 Promapp 的 Azure AD 单一登录，需要完成以下步骤：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** ，为用户启用该功能。
2. 在应用程序端[配置 Promapp 单一登录](#configure-promapp-single-sign-on)  。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录  。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，为用户启用 Azure AD 单一登录  。
5. **[测试单一登录](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录。

要为 Promapp 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Promapp”应用程序集成页上，选择“单一登录”： 

    ![选择“单一登录”](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录：  

    ![选择单一登录方法](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框：   

    ![“编辑”图标](common/edit-urls.png)

4. 若要在 IdP 发起的模式下配置应用程序，请在“基本 SAML 配置”对话框中完成以下步骤。 

    ![“基本 SAML 配置”对话框](common/idp-intiated.png)

    1. 在“标识符”框中，输入采用以下模式的 URL： 

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > 目前，Azure AD 与 Promapp 的集成仅被配置用于服务发起的身份验证。 （也就是说，转到 Promapp URL 即可启动身份验证过程。）但是，“回复 URL”字段是必填字段。 

    1. 在“回复 URL”框中，输入采用以下模式的 URL： 

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. 如果要在 SP 发起的模式下配置应用程序，选择“设置其他 URL”  。 在“登录 URL”框中，输入以下模式的 URL  ：

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![Promapp 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > 这些值是占位符。 需要使用实际的标识符、回复 URL 和登录 URL。 若要获取这些值，请与 [Promapp 支持团队](https://www.promapp.com/about-us/contact-us/)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”对话框中显示的模式  。

6. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，选择“证书(Base64)”旁的“下载”链接（根据要求），并将证书保存在计算机上     ：

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Promapp”部分，根据要求复制相应的 URL： 

    ![复制配置 URL](common/copy-configuration-urls.png)

    1. **登录 URL**。

    1. **Azure AD 标识符**。

    1. **注销 URL**。

### <a name="configure-promapp-single-sign-on"></a>配置 Promapp 单一登录

1. 以管理员身份登录到 Promapp 公司站点。

2. 在窗口顶部的菜单中，选择“管理员”  ：
   
    ![选择“管理员”][12]

3. 选择“配置”  ：
   
    ![选择“配置”][13]

4. 在“安全性”对话框中执行以下步骤。 
   
    ![“安全性”对话框][14]
    
    1. 在“SSO 登录 URL”框中，粘贴从 Azure 门户复制的“登录 URL”   。
    
    1. 在“SSO - 单一登录模式”列表中，选择“可选”。   选择“保存”。 

       > [!NOTE]
       > “可选”模式仅用于测试。 对配置感到满意后，请在“SSO - 单一登录模式”列表中选择“必需”  ，强制所有用户使用 Azure AD 进行身份验证。 

    1. 在记事本中，打开在上一部分下载的证书。 复制除第一行 ( **-----BEGIN CERTIFICATE-----** ) 和最后一行 ( **-----END CERTIFICATE-----** ) 之外的证书内容。 将证书内容粘贴到“SSO-x.509 证书”  框中，然后选择“保存”。 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”：   

    ![选择“所有用户”](common/users.png)

2. 选择屏幕顶部的“新建用户”： 

    ![选择“新建用户”](common/new-user.png)

3. 在“用户”对话框中执行以下步骤： 

    ![“用户”对话框](common/user-properties.png)

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入 BrittaSimon@\<yourcompanydomain>.\<extension>   。 （例如：BrittaSimon@contoso.com。）

    1. 选择“显示密码”，然后记下“密码”框中的值   。

    1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Promapp 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Promapp”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“Promapp”。 

    ![应用程序列表](common/all-applications.png)

3. 在左窗格中选择“用户和组”： 

    ![选择“用户和组”](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”按钮。   

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  单击屏幕底部的“选择”按钮  。

7. 在“添加分配”对话框中选择“分配”。  

### <a name="just-in-time-user-provisioning"></a>恰时用户预配

Promapp 支持恰时用户预配。 此功能默认启用。 如果 Promapp 中尚不存在用户，身份验证后会创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录

现在，需要使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Promapp”磁贴时，应会自动登录到设置了 SSO 的 Promapp 实例。 有关访问面板的详细信息，请参阅[在“我的应用”门户中访问和使用应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
