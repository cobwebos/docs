---
title: 教程：Azure Active Directory 与 RolePoint 的集成 | Microsoft Docs
description: 本教程介绍如何在 Azure Active Directory 与 RolePoint 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68d37f40-15da-45f5-a9e1-d53f78e786d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fd17d2f8577532778733866260f43e9ac7685
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092734"
---
# <a name="tutorial-azure-active-directory-integration-with-rolepoint"></a>教程：Azure Active Directory 与 RolePoint 的集成

本教程介绍了如何将 RolePoint 与 Azure Active Directory (Azure AD) 进行集成。
这种集成可提供以下优势：

* 可使用 Azure AD 控制谁有权访问 RolePoint。
* 可让用户使用其 Azure AD 帐户自动登录到 RolePoint（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 RolePoint 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录的 RolePoint 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD 单一登录。

* RolePoint 支持 SP 发起的 SSO。

## <a name="add-rolepoint-from-the-gallery"></a>从库中添加 RolePoint

要设置 RolePoint 与 Azure AD 的集成，需要从库中将 RolePoint 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”： 

    ![选择“Azure Active Directory”](common/select-azuread.png)

2. 转到“企业应用程序” > “所有应用程序”：

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加应用程序，请在窗口顶部选择“新建应用程序”  ：

    ![选择“新建应用程序”](common/add-new-app.png)

4. 在搜索框中输入 **RolePoint**。 在搜索结果中选择“RolePoint”，然后选择“添加”。  

     ![搜索结果](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将使用名为 Britta Simon 的测试用户来配置并测试 RolePoint 的 Azure AD 单一登录。
若要启用单一登录，需要在 Azure AD 用户与 RolePoint 中的对应用户之间建立关系。

若要配置并测试 RolePoint 的 Azure AD 单一登录，需要完成以下步骤：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** ，为用户启用该功能。
2. 在应用程序端[配置 RolePoint 单一登录](#configure-rolepoint-single-sign-on)  。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录  。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，为用户启用 Azure AD 单一登录  。
5. 创建与用户的 Azure AD 表示形式相链接的 [RolePoint 测试用户](#create-a-rolepoint-test-user)  。
6. **[测试单一登录](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 RolePoint 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“RolePoint”应用程序集成页上，选择“单一登录”： 

    ![选择“单一登录”](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录：  

    ![选择单一登录方法](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框：   

    ![“编辑”图标](common/edit-urls.png)

4. 在“基本 SAML 配置”对话框中执行以下步骤。 

    ![“基本 SAML 配置”对话框](common/sp-identifier.png)

    1. 在“登录 URL”框中，输入以下模式的 URL  ：

       `https://<subdomain>.rolepoint.com/login`

    1. 在“标识符(实体 ID)”框中，输入以下模式的 URL  ：

       `https://app.rolepoint.com/<instancename>`

    > [!NOTE]
    > 这些值是占位符。 需要使用实际登录 URL 和标识符。 建议在标识符中使用唯一的字符串值。 请联系 [RolePoint 支持团队](mailto:info@rolepoint.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”对话框中显示的模式  。

5. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，根据要求选择“联合元数据 XML”旁边的“下载”链接，并将文件保存在计算机上。    

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 RolePoint”部分，根据要求复制相应的 URL： 

    ![复制配置 URL](common/copy-configuration-urls.png)

    1. **登录 URL**。

    1. **Azure AD 标识符**。

    1. **注销 URL**。


### <a name="configure-rolepoint-single-sign-on"></a>配置 RolePoint 单一登录

若要在 RolePoint 端设置单一登录，需联系 [RolePoint 支持团队](mailto:info@rolepoint.com)。 向该团队发送“联合元数据 XML”文件以及从 Azure 门户获得的 URL。 他们将配置 RolePoint 角色，确保在两端正确设置 SAML SSO 连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”：   

    ![选择“所有用户”](common/users.png)

2. 选择窗口顶部的“新建用户”： 

    ![选择“新建用户”](common/new-user.png)

3. 在“用户”对话框中执行以下步骤： 

    ![“用户”对话框](common/user-properties.png)

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入 BrittaSimon@\<yourcompanydomain>.\<extension>。 （例如：BrittaSimon@contoso.com。）

    1. 选择“显示密码”，然后记下“密码”框中的值   。

    1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 RolePoint 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“RolePoint”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“RolePoint”  。

    ![应用程序列表](common/all-applications.png)

3. 在左窗格中选择“用户和组”： 

    ![选择“用户和组”](common/users-groups-blade.png)

4. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![选择“添加用户”](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击窗口底部的“选择”按钮。   

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  单击窗口底部的“选择”按钮  。

7. 在“添加分配”对话框中选择“分配”。  

### <a name="create-a-rolepoint-test-user"></a>创建 RolePoint 测试用户

接下来，需要在 RolePoint 中创建一个名为 Britta Simon 的用户。 联系  [RolePoint 支持团队](mailto:info@rolepoint.com)，向 RolePoint 添加用户。 使用单一登录前，需先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

现在，需要使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“RolePoint”磁贴时，应会自动登录到设置了 SSO 的 RolePoint 实例。 有关访问面板的详细信息，请参阅[在“我的应用”门户中访问和使用应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
