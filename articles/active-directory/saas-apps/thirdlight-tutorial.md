---
title: 教程：Azure Active Directory 与 ThirdLight 集成 | Microsoft Docs
description: 本教程介绍如何在 Azure Active Directory 与 ThirdLight 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 67c8dcfffd78d4c0114a96622235d6548627fa92
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236961"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>教程：Azure Active Directory 与 ThirdLight 集成

本教程介绍如何将 ThirdLight 与 Azure Active Directory (Azure AD) 集成。 这种集成可提供以下优势：

* 可使用 Azure AD 控制谁有权访问 ThirdLight。
* 可让用户使用其 Azure AD 帐户自动登录到 ThirdLight（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 ThirdLight 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 ThirdLight 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

* ThirdLight 支持 SP 发起的 SSO。

## <a name="add-thirdlight-from-the-gallery"></a>从库中添加 ThirdLight

若要设置 ThirdLight 与 Azure AD 的集成，需要从库中将 ThirdLight 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”： 

    ![选择“Azure Active Directory”](common/select-azuread.png)

2. 转到“企业应用程序” > “所有应用程序”   ：

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加应用程序，请在窗口顶部选择“新建应用程序”  ：

    ![选择“新建应用程序”](common/add-new-app.png)

4. 在“搜索”框中，输入“ThirdLight”  。 在搜索结果中选择“ThirdLight”，然后选择“添加”。  

     ![搜索结果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将使用名为 Britta Simon 的测试用户来配置并测试 ThirdLight 的 Azure AD 单一登录。
若要启用单一登录，需要在 Azure AD 用户与 ThirdLight 中的对应用户之间建立关系。

若要配置并测试 ThirdLight 的 Azure AD 单一登录，需要完成以下步骤：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** ，为用户启用该功能。
2. 在应用程序端[配置 ThirdLight 单一登录](#configure-thirdlight-single-sign-on)  。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录  。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，为用户启用 Azure AD 单一登录  。
5. 创建与用户的 Azure AD 表示形式相链接的 [ThirdLight 测试用户](#create-a-thirdlight-test-user)  。
6. **[测试单一登录](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 ThirdLight 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“ThirdLight”应用程序集成页上，选择“单一登录”： 

    ![选择“单一登录”](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录：  

    ![选择单一登录方法](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框：   

    ![“编辑”图标](common/edit-urls.png)

4. 在“基本 SAML 配置”对话框中完成以下步骤  。

    ![“基本 SAML 配置”对话框](common/sp-identifier.png)

    1. 在“登录 URL”框中，输入以下模式的 URL  ：
    
          `https://<subdomain>.thirdlight.com/`

    1. 在“标识符(实体 ID)”框中，输入以下模式的 URL  ：

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > 这些值是占位符。 需要使用实际登录 URL 和标识符。 若要获取这些值，请与 [ThirdLight 支持团队](https://www.thirdlight.com/support)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”对话框中显示的模式  。

5. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，根据要求选择“联合元数据 XML”旁边的“下载”链接，并将文件保存在计算机上：    

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 ThirdLight”部分，根据要求复制相应的 URL： 

    ![复制配置 URL](common/copy-configuration-urls.png)

    1. **登录 URL**。

    1. **Azure AD 标识符**。

    1. **注销 URL**。

### <a name="configure-thirdlight-single-sign-on"></a>配置 ThirdLight 单一登录

1. 在新的 Web 浏览器窗口中，以管理员身份登录到 ThirdLight 公司站点。

1. 转到“配置”   > “系统管理”   >   “SAML2”：

    ![系统管理](./media/thirdlight-tutorial/ic805843.png "Administration")

1. 在 SAML2 配置部分中，执行以下步骤。
  
    ![SAML2 配置部分](./media/thirdlight-tutorial/ic805844.png "SAML2 配置部分")

    1. 选择“启用 SAML2 单一登录”  。

    1. 在“IdP 元数据的源”下选择“从 XML 加载 IdP 元数据”   。

    1. 打开在上一部分从 Azure 门户下载的元数据文件。 复制文件的内容，将其粘贴到“IdP 元数据 XML”框中  。

    1. 选择“保存 SAML2 设置”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”：   

    ![选择“所有用户”](common/users.png)

2. 选择窗口顶部的“新建用户”： 

    ![选择“新建用户”](common/new-user.png)

3. 在“用户”对话框中执行以下步骤： 

    ![“用户”对话框](common/user-properties.png)

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入 BrittaSimon@\<yourcompanydomain>.\<extension>   。 （例如：BrittaSimon@contoso.com。）

    1. 选择“显示密码”，然后记下“密码”框中的值   。

    1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 ThirdLight 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“ThirdLight”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“ThirdLight”  。

    ![应用程序列表](common/all-applications.png)

3. 在左窗格中选择“用户和组”： 

    ![选择“用户和组”](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击窗口底部的“选择”按钮。   

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  单击窗口底部的“选择”按钮  。

7. 在“添加分配”对话框中选择“分配”。  

### <a name="create-a-thirdlight-test-user"></a>创建 ThirdLight 测试用户

若要使 Azure AD 用户能够登录到 ThirdLight，需要将其添加到 ThirdLight 中。 需要手动添加它们。

若要创建用户帐户，请执行以下步骤：

1. 以管理员身份登录到 ThirdLight 公司站点。

1. 转到“用户”  选项卡。

1. 选择“用户和组”  。

1. 选择“添加新用户”。 

1. 输入要预配的有效 Azure AD 帐户的用户名、名称或说明，以及电子邮件地址。 选择新成员的预设或组。

1. 选择“创建”  。

> [!NOTE]
> 可以使用 ThirdLight 提供的任何用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

现在，需要使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“ThirdLight”磁贴时，应会自动登录到设置了 SSO 的 ThirdLight 实例。 有关访问面板的详细信息，请参阅[在“我的应用”门户中访问和使用应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
