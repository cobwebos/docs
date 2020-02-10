---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Nintex Promapp 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Nintex Promapp 之间配置单一登录。
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
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984382"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Nintex Promapp 的集成

本教程介绍如何将 Nintex Promapp 与 Azure Active Directory (Azure AD) 集成。 将 Nintex Promapp 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Nintex Promapp。
* 让用户使用其 Azure AD 帐户自动登录到 Nintex Promapp。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Nintex Promapp 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Nintex Promapp 支持 **SP 和 IDP** 发起的 SSO
* Nintex Promapp 支持**实时**用户预配

## <a name="adding-nintex-promapp-from-the-gallery"></a>从库中添加 Nintex Promapp

若要配置 Nintex Promapp 与 Azure AD 的集成，需要从库中将 Nintex Promapp 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Nintex Promapp**。 
1. 在结果面板中选择“Nintex Promapp”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>配置并测试 Nintex Promapp 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Nintex Promapp 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Nintex Promapp 中的相关用户之间建立链接关系。

若要配置并测试 Nintex Promapp 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Nintex Promapp SSO](#configure-nintex-promapp-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Nintex Promapp 测试用户](#create-nintex-promapp-test-user)** - 在 Nintex Promapp 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Nintex Promapp”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

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
       > 目前，Azure AD 与 Nintex Promapp 的集成仅配置用于服务发起的身份验证。 （也就是说，转到 Nintex Promapp URL 即可启动身份验证过程。）但是，“回复 URL”字段是必填字段。 

    1. 在“回复 URL”框中，输入采用以下模式的 URL： 

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”框中，输入以下模式的 URL  ：`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > 这些值是占位符。 需要使用实际的标识符、回复 URL 和登录 URL。 请联系 [Nintex Promapp 客户端支持团队](https://www.promapp.com/about-us/contact-us/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”对话框中显示的模式  。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Nintex Promapp”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Nintex Promapp 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Nintex Promapp”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-nintex-promapp-sso"></a>配置 Nintex Promapp SSO

1. 以管理员身份登录到 Nintex Promapp 公司站点。

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

### <a name="create-nintex-promapp-test-user"></a>创建 Nintex Promapp 测试用户

在本部分，我们将在 Nintex Promapp 中创建名为 B.Simon 的用户。 Nintex Promapp 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Nintex Promapp 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Nintex Promapp”磁贴时，应会自动登录到设置了 SSO 的 Nintex Promapp。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Nintex Promapp](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png