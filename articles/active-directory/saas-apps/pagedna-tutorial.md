---
title: 教程：Azure Active Directory 与 PageDNA 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 PageDNA 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227469"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>教程：Azure Active Directory 与 PageDNA 的集成

本教程介绍如何将 PageDNA 与 Azure Active Directory (Azure AD) 集成。

将 PageDNA 与 Azure AD 集成可提供以下优势：

* 在 Azure AD 中，可以控制谁有权访问 PageDNA。
* 可让用户使用其 Azure AD 帐户自动登录到 PageDNA（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 PageDNA 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 PageDNA 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置和测试 Azure AD 单一登录，并将 PageDNA 与 Azure AD 集成。

PageDNA 支持以下功能：

* SP 发起的单一登录 (SSO)。

* 实时用户预配。

## <a name="add-pagedna-from-the-azure-marketplace"></a>从 Azure 市场添加 PageDNA

若要配置 PageDNA 与 Azure AD 的集成，需要从 Azure 市场中将 PageDNA 添加到托管 SaaS 应用列表：

1. 登录到 [Azure 门户](https://portal.azure.com?azure-portal=true)。
1. 在左窗格中选择“Azure Active Directory”  。

    ![“Azure Active Directory”选项](common/select-azuread.png)

1. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加新应用程序，请在窗格顶部选择“+ 新建应用程序”  。

    ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中，输入“PageDNA”  。 在搜索结果中选择“PageDNA”，然后选择“添加”以添加应用程序   。

    ![结果列表中的“PageDNA”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 Britta Simon  的测试用户来配置并测试 PageDNA 的 Azure AD 单一登录。 若要正常使用单一登录，需要在 Azure AD 用户与 PageDNA 相关用户之间建立链接。

若要配置并测试 PageDNA 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** ，让用户能够使用此功能。
1. [配置 PageDNA 单一登录](#configure-pagedna-single-sign-on)  ，在应用程序端配置单一登录。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
1. [创建 PageDNA 测试用户](#create-a-pagedna-test-user)  ，以便在 PageDNA 中创建名为 Britta Simon 的用户，该用户与名为 Britta Simon 的 Azure AD 用户相关联。
1. **[测试单一登录](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 PageDNA 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“PageDNA”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML/WS-Fed”模式以启用单一登录   。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）可打开“基本 SAML 配置”窗格    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”窗格中执行以下步骤  ：

    ![PageDNA 域和 URL 单一登录信息](common/sp-identifier.png)

    1. 在“登录 URL”  框中，使用以下模式之一输入 URL：

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. 在“标识符(实体 ID)”  框中，使用以下模式之一输入 URL：

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请联系 [PageDNA 支持团队](mailto:success@pagedna.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”  窗格中显示的模式。

1. 在“设置 SAML 单一登录”窗格上的“SAML 签名证书”部分，选择“下载”以从给定的选项下载“证书(原始)”并将其保存在计算机中     。

    ![“证书(原始)”下载选项](common/certificateraw.png)

1. 在“设置 PageDNA”部分，复制所需的一个或多个 URL  ：

   * **登录 URL**
   * **Azure AD 标识符**
   * **注销 URL**

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>配置 PageDNA 单一登录

若要在 PageDNA 端配置单一登录，请将下载的“证书(原始)”以及从 Azure 门户复制的相应 URL 发送给 [PageDNA 支持团队](mailto:success@pagedna.com)。 PageDNA 团队将确保在两端正确设置 SAML SSO 连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”   > “用户” > “所有用户”。   

    ![“用户”和“所有用户”选项](common/users.png)

1. 在屏幕顶部选择“+ 新建用户”  。

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中执行以下步骤  ：

    ![“用户”窗格](common/user-properties.png)

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入 BrittaSimon\@\<yourcompanydomain>.\<extension>   。 例如，“BrittaSimon\@contoso.com”  。

    1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  

    1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予用户 Britta Simon 访问 PageDNA 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” > “PageDNA”。   

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在“应用程序”列表中，选择“PageDNA”。 

    ![应用程序列表中的 PageDNA](common/all-applications.png)

1. 在左窗格的“管理”下，选择“用户和组”   。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“+ 添加用户”，然后在“添加分配”窗格中选择“用户和组”    。

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，选择“用户”列表中的“Britta Simon”，然后选择窗格底部的“选择”     。

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相应的角色  。 在窗格底部选择“选择”  。

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-a-pagedna-test-user"></a>创建 PageDNA 测试用户

现在在 PageDNA 中创建名为 Britta Simon 的用户。 无需执行任何操作来创建该用户。 PageDNA 支持默认已启用的实时用户预配。 如果 PageDNA 中尚不存在名为 B. Simon 的用户，则会在身份验证后创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“PageDNA”时，应自动登录到为其设置了单一登录的 PageDNA 订阅  。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

* [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

