---
title: 教程：Azure Active Directory 与 Uberflip 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Uberflip 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048476"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>教程：Azure Active Directory 与 Uberflip 的集成

本教程介绍了如何将 Uberflip 与 Azure Active Directory (Azure AD) 进行集成。

将 Uberflip 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Uberflip
* 可让用户使用其 Azure AD 帐户自动登录到 Uberflip（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Uberflip 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 Uberflip 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

Uberflip 支持以下功能：

* SP 启动和 IDP 启动的单一登录 (SSO)。
* 实时用户预配。

## <a name="add-uberflip-from-the-azure-marketplace"></a>从 Azure 市场添加 Uberflip

若要配置 Uberflip 与 Azure AD 的集成，需要从 Azure 市场中将 Uberflip 添加到托管 SaaS 应用列表。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中选择“Azure Active Directory”  。

   ![“Azure Active Directory”选项](common/select-azuread.png)

1. 转到“企业应用程序”，并选择“所有应用程序”。  

   ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加新应用程序，请在窗格顶部选择“+ 新建应用程序”  。

   ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中，输入“Uberflip”  。 在搜索结果中选择“Uberflip”，然后选择“添加”以添加应用程序   。

   ![结果列表中的 Uberflip](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **B Simon** 的测试用户配置和测试 Uberflip 的 Azure AD 单一登录。 若要正常使用单一登录，需要在 Azure AD 用户与 Uberflip 相关用户之间建立链接关系。

若要配置和测试 Uberflip 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** ，让用户能够使用此功能。
1. **[配置 Uberflip 单一登录](#configure-uberflip-single-sign-on)** 以在应用程序端配置单一登录设置。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B. Simon 测试 Azure AD 单一登录。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，以使 B. Simon 能够使用 Azure AD 单一登录。
1. **[创建 Uberflip 测试用户](#create-an-uberflip-test-user)** ，以便在 Uberflip 中创建名为 B. Simon 的用户，该用户与名为 B. Simon 的 Azure AD 用户相关联。
1. **[测试单一登录](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Uberflip 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Uberflip”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML/WS-Fed”模式以启用单一登录   。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）可打开“基本 SAML 配置”窗格    。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”窗格中，根据需要配置的 SSO 模式，执行以下步骤之一  ：

   * 要在 IDP 启动的 SSO 模式下配置应用程序，请在“回复 URL (断言使用者服务 URL)”框中，使用以下模式输入 URL  ：

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip 域和 URL 单一登录信息](common/both-replyurl.png)

     > [!NOTE]
     > 此值不是真实值。 请使用实际回复 URL 更新此值。 若要获取实际值，请与 [Uberflip 支持团队](mailto:support@uberflip.com)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  窗格中显示的模式。

   * 要在 SP 启动的 SSO 模式下配置应用程序，请选择“设置其他 URL”，然后在登录 URL 框中输入此 URL   ：

     `https://app.uberflip.com/users/login`

     ![Uberflip 域和 URL 单一登录信息](common/both-signonurl.png)

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以从给定的选项下载“联合元数据 XML”并将其保存在计算机中     。

   ![联合元数据 XML 下载选项](common/metadataxml.png)

1. 在“设置 Uberflip”窗格中，复制所需的一个或多个 URL  ：

   * **登录 URL**
   * **Azure AD 标识符**
   * **注销 URL**

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>配置 Uberflip 单一登录

若要在 Uberflip 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Uberflip 支持团队](mailto:support@uberflip.com)。 Uberflip 团队将确保在两端正确设置 SAML SSO 连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 B. Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。

    ![“用户”和“所有用户”选项](common/users.png)

1. 在屏幕顶部选择“+ 新建用户”  。

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中执行以下步骤  ：

    ![“用户”窗格](common/user-properties.png)

    1. 在“名称”框中，输入“BSimon”   。
  
    1. 在“用户名”框中，输入“BSimon\@\<yourcompanydomain>.\<extension>”。 例如，BSimon\@contoso.com。

    1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  

    1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B. Simon 访问 Uberflip 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” > “Uberflip”。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Uberflip”。 

    ![应用程序列表中的 Uberflip](common/all-applications.png)

1. 在左窗格的“管理”下，选择“用户和组”   。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“+ 添加用户”，然后在“添加分配”窗格中选择“用户和组”    。

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，选择“用户”列表中的“B Simon”，然后选择窗格底部的“选择”     。

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相应的角色  。 在窗格底部选择“选择”  。

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-an-uberflip-test-user"></a>创建 Uberflip 测试用户

名为 B. Simon 的用户现在 Uberflip 中创建。 无需执行任何操作来创建该用户。 Uberflip 支持默认启用的实时用户预配。 如果 Uberflip 中尚不存在名为 B. Simon 的用户，则会在身份验证后创建一个新用户。

> [!NOTE]
> 如需手动创建用户，请联系 [Uberflip 支持团队](mailto:support@uberflip.com)。

### <a name="test-single-sign-on"></a>测试单一登录

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“Uberflip”时，应自动登录到你为其设置单一登录的 Uberflip 订阅  。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

* [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
