---
title: 教程：Azure Active Directory 与 Heroku 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Heroku 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a9cb7738a8983ca2f76866fa40d49cd6cc06f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159085"
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>教程：Azure Active Directory 与 Heroku 集成

本教程介绍了如何将 Heroku 与 Azure Active Directory (Azure AD) 进行集成。
将 Heroku 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Heroku。
* 可以让用户使用其 Azure AD 帐户自动登录到 Heroku（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Heroku 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Heroku 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Heroku 支持 SP 发起的 SSO 
* Heroku 支持“恰时”用户预配 

## <a name="adding-heroku-from-the-gallery"></a>从库中添加 Heroku

要配置 Heroku 与 Azure AD 的集成，需要将库中的 Heroku 添加到托管的 SaaS 应用列表。

**若要从库中添加 Heroku，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Heroku”，在结果面板中选择“Heroku”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Heroku](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Heroku 的 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 Heroku 中的相关用户之间建立链接关系。

若要配置和测试 Heroku 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Heroku 单一登录](#configure-heroku-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **创建 Heroku 测试用户[ - 在 Heroku 中创建 Britta Simon 的对应用户，并且将其链接到用户在 Azure AD 中的代表](#create-heroku-test-user)** 。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Heroku 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 **Heroku** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Heroku 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://sso.heroku.com/saml/<company-name>/init`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 可从 Heroku 团队获取这些值，本文的稍后部分将对此进行描述。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Heroku”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-heroku-single-sign-on"></a>配置 Heroku 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Heroku 租户。

2. 单击“设置”选项卡。 

3. 在“单一登录”页面上，单击“上载元数据”。  

4. 上传从 Azure 门户下载的元数据文件。

5. 安装成功后，管理员会看到一个确认对话框，其中显示最终用户的 SSO 登录 URL。

6. 复制“Heroku 登录 URL”和“Heroku 实体 ID”值，返回 Azure 门户中的“基本 SAML 配置”部分，并分别将这两个值粘贴到“登录 URL”和“标识符(实体 ID)”文本框中      。

    ![配置单一登录](./media/heroku-tutorial/tutorial_heroku_52.png)

7. 单击“下一步”。 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Heroku 的访问权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Heroku”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Heroku”  。

    ![应用程序列表中的 Heroku 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-heroku-test-user"></a>创建 Heroku 测试用户

在本部分中，会在 Heroku 中创建一个名为“Britta Simon”的用户。 Heroku 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 访问 Heroku 时，如果该用户尚不存在，则将创建新用户。 预配帐户后，最终用户会收到一封验证电子邮件，需单击确认链接。

> [!NOTE]
> 如果需要手动创建用户，则需要联系 [Heroku 客户端支持团队](https://www.heroku.com/support)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Heroku 磁贴时，应当会自动登录到为其设置了 SSO 的 Heroku。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

