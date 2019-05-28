---
title: 教程：Azure Active Directory 与 Adaptive Insights 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adaptive Insights 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2c7ca8699274b96f7f382dfe1958bf5babbbe99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720174"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>教程：Azure Active Directory 与 Adaptive Insights 集成

本教程介绍如何将 Adaptive Insights 与 Azure Active Directory (Azure AD) 集成。
将 Adaptive Insights 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Adaptive Insights。
* 可以让用户使用其 Azure AD 帐户自动登录到 Adaptive Insights（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Adaptive Insights 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Adaptive Insights 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Adaptive Insights 支持启用了 **IDP** 的 SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>从库中添加 Adaptive Insights

若要配置 Adaptive Insights 与 Azure AD 的集成，需要从库中将 Adaptive Insights 添加到托管 SaaS 应用列表。

**若要从库中添加 Adaptive Insights，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Adaptive Insights”，在结果面板中选择“Adaptive Insights”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Adaptive Insights](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Adaptive Insights 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Adaptive Insights 相关用户之间建立链接关系。

若要配置和测试 Adaptive Insights 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Adaptive Insights 单一登录](#configure-adaptive-insights-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Adaptive Insights 测试用户](#create-adaptive-insights-test-user)** - 在 Adaptive Insights 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要配置 Adaptive Insights 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在  Adaptive Insights 应用程序集成页上，单击“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    ![Adaptive Insights 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > 可以从 Adaptive Insights 的“SAML SSO 设置”  页获得“标识符(实体 ID)”和“回复 URL”值。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Adaptive Insights”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-adaptive-insights-single-sign-on"></a>配置 Adaptive Insights 单一登录

1. 在其他 Web 浏览器窗口中，以管理员身份登录 Adaptive Insights 公司站点。

2. 转到“管理员”。 

    ![管理员](./media/adaptivesuite-tutorial/ic805644.png "管理员")

3. 在“用户和角色”  部分中，单击“管理 SAML SSO 设置”  。

    ![管理 SAML SSO 设置](./media/adaptivesuite-tutorial/ic805645.png "管理 SAML SSO 设置")

4. 在“SAML SSO 设置”  页上，执行以下步骤：

    ![SAML SSO 设置](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO 设置")

    a. 在“标识提供者名称”  文本框中，键入配置名称。

    b. 将从 Azure 门户复制的“Azure AD 标识符”  值粘贴到“标识提供者实体 ID”  文本框。

    c. 将从 Azure 门户复制的“登录 URL”  值粘贴到“标识提供者程序 SSO URL”  文本框。

    d. 将从 Azure 门户复制的“注销 URL”  值粘贴到“自定义注销 URL”  文本框。

    e. 若要上传所下载的证书，请单击“选择文件”  。

    f. 针对各对象，选择以下项：

     * 对于“SAML 用户 ID”  ，请选择“用户的 Adaptive Insights 用户名称”  。

     * 对于“SAML 用户 ID 位置”  ，请选择“主题 NameID 中的用户 ID”  。

     * 对于“SAML NameID 格式”  ，请选择“电子邮件地址”  。

     * 对于“启用 SAML”  ，请选择“允许 SAML SSO 和直接 Adaptive Insights 登录”  。

    g. 复制 **Adaptive Insights SSO URL** 并粘贴到 Azure 门户中“Adaptive Insights 域和 URL”  部分的“标识符(实体 ID)”  和“回复 URL”  文本框中。

    h. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon@yourcompanydomain.extension  。 例如，BrittaSimon@contoso.com。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Adaptive Insights 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Adaptive Insights”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Adaptive Insights”  。

    ![应用程序列表中的 Adaptive Insights 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-adaptive-insights-test-user"></a>创建 Adaptive Insights 测试用户

要使 Azure AD 用户能够登录 Adaptive Insights，必须将其预配到 Adaptive Insights 中。 就 Adaptive Insights 来说，预配任务需要手动完成。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录 Adaptive Insights 公司网站  。

2. 转到“管理员”。 

   ![管理员](./media/adaptivesuite-tutorial/IC805644.png "管理员")

3. 在“用户和角色”  部分中，单击“添加用户”  。

   ![添加用户](./media/adaptivesuite-tutorial/IC805648.png "添加用户")

4. 在“新建用户”  部分中，执行以下步骤：

   ![提交](./media/adaptivesuite-tutorial/IC805649.png "提交")

   a. 键入希望在相关文本框中预配的有效 Azure Active Directory 用户的“姓名”  、“登录名”  、“电子邮件”  、“密码”  。

   b. 选择“角色”  。

   c. 单击“提交”  。

> [!NOTE]
> 可以使用其他任何 Adaptive Insights 用户帐户创建工具或 Adaptive Insights 提供的 API 来预配 AAD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Adaptive Insights 磁贴时，应会自动登录到为其设置了 SSO 的 Adaptive Insights。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)