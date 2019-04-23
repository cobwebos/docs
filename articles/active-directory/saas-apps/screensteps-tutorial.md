---
title: 教程：Azure Active Directory 与 ScreenSteps 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ScreenSteps 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 570f789d0f399c5ffa7535101136ab65ba58ccd5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59277079"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>教程：Azure Active Directory 与 ScreenSteps 集成

本教程介绍如何将 ScreenSteps 与 Azure Active Directory (Azure AD) 集成。
将 ScreenSteps 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 ScreenSteps。
* 可让用户使用其 Azure AD 帐户自动登录到 ScreenSteps（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 ScreenSteps 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 ScreenSteps 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* ScreenSteps 支持 SP 发起的 SSO

## <a name="adding-screensteps-from-the-gallery"></a>从库中添加 ScreenSteps

要配置 ScreenSteps 与 Azure AD 的集成，需要从库中将 ScreenSteps 添加到托管 SaaS 应用列表。

**要从库添加 ScreenSteps，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“ScreenSteps”，在结果面板中选择“ScreenSteps”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 ScreenSteps](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 ScreenSteps 的 Azure AD 单一登录。
要使单一登录有效，需要在 Azure AD 用户与 ScreenSteps 相关用户之间建立链接关系。

要配置并测试 ScreenSteps 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 ScreenSteps 单一登录](#configure-screensteps-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 ScreenSteps 测试用户](#create-screensteps-test-user) - 在 ScreenSteps 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要配置 ScreenSteps 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 ScreenSteps 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![ScreenSteps 域和 URL 单一登录信息](common/sp-signonurl.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > 此值不是真实值。 本教程稍后将介绍如何使用实际登录 URL 来更新该值。

5. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 ScreenSteps”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-screensteps-single-sign-on"></a>配置 ScreenSteps 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 ScreenSteps 公司站点。

1. 单击“帐户设置”。

    ![帐户管理](./media/screensteps-tutorial/ic778523.png "帐户管理")

1. 单击“单一登录”。

    ![远程身份验证](./media/screensteps-tutorial/ic778524.png "远程身份验证")

1. 单击“创建单一登录终结点”。

    ![远程身份验证](./media/screensteps-tutorial/ic778525.png "远程身份验证")

1. 在“创建单一登录终结点”部分中，执行以下步骤：

    ![创建身份验证终结点](./media/screensteps-tutorial/ic778526.png "创建身份验证终结点")

    a. 在“标题”文本框中，键入标题。

    b. 从“模式”列表中，选择“SAML”。

    c. 单击“创建”。

1. “编辑”新的终结点。

    ![编辑终结点](./media/screensteps-tutorial/ic778528.png "Edit endpoint")

1. 在“编辑单一登录终结点”部分中，执行以下步骤：

    ![远程身份验证终结点](./media/screensteps-tutorial/ic778527.png "远程身份验证终结点")

    a. 单击“上传新 SAML 证书文件”，然后上传从 Azure 门户下载的证书。

    b. 在“远程登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 选择用户预配后会被分配到的**组**。

    e. 单击“更新”。

    f. 将“SAML 使用者 URL”复制到剪贴板，然后粘贴到 Azure 门户中“基本 SAML 配置”部分中的“登录 URL”文本框中。

    g. 返回“编辑单一登录终结点”。

    h. 单击“对帐户设为默认”按钮，对登录到 ScreenSteps 的所有用户使用此终结点。 或者可单击“添加到站点”按钮，对 **ScreenSteps** 中的特定站点使用此终结点。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予 ScreenSteps 的访问权限使之能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“ScreenSteps”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“ScreenSteps”。

    ![应用程序列表中的 ScreenSteps 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-screensteps-test-user"></a>创建 ScreenSteps 测试用户

在本部分中，将在 ScreenSteps 中创建名为 Britta Simon 的用户。 与  [ScreenSteps 客户端支持团队](https://www.screensteps.com/contact) 合作，在 ScreenSteps 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 ScreenSteps 磁贴时，应当会自动登录到为其设置了 SSO 的 ScreenSteps。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)