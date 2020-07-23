---
title: 教程：Azure Active Directory 与 N2F - Expense reports 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 N2F - Expense reports 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161283"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>教程：Azure Active Directory 与 N2F - Expense reports 的集成

本教程介绍如何将 N2F - Expense reports 与 Azure Active Directory (Azure AD) 集成。
将 N2F - Expense reports 与 Azure AD 集成可带来以下优势：

* 可在 Azure AD 中控制谁有权访问 N2F - Expense reports。
* 可让用户使用其 Azure AD 帐户自动登录到 N2F - Expense reports（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 N2F - Expense reports 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 N2F - Expense reports 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* N2F - Expense reports 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>从库中添加 N2F - Expense reports

要配置 N2F - Expense reports 与 Azure AD 的集成，需要从库中将 N2F - Expense reports 添加到托管 SaaS 应用列表。

**若要从库中添加 N2F - Expense reports，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **N2F - Expense reports**，在结果面板中选择“N2F - Expense reports”，然后单击“添加”按钮添加该应用程序。  

     ![结果列表中的 N2F - Expense reports](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 N2F - Expense reports 的 Azure AD 单一登录。 
若要运行单一登录，需要在 Azure AD 用户与 N2F - Expense reports 相关用户之间建立链接关系。

若要配置和测试 N2F - Expense reports 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 N2F - Expense reports 单一登录](#configure-n2f---expense-reports-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 N2F - Expense reports 测试用户](#create-n2f---expense-reports-test-user)** - 在 N2F - Expense reports 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 N2F - Expense reports 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“N2F - Expense reports”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分，如果你要在“IDP 发起的模式”下配置应用程序，用户无需执行任何步骤，因为应用已与 Azure 进行预集成。  

    ![N2F - Expense reports 域和 URL 单一登录信息](common/preintegrated.png)

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![N2F - Expense reports 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 URL：`https://www.n2f.com/app/`

6. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

7. 在“设置 myPolicies”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>配置 N2F - Expense reports 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 N2F - Expense reports 公司站点。

2. 单击“设置”，然后从下拉列表中选择“高级设置”。  

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure1.png)

3. 选择“帐户设置”选项卡。 

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure2.png)

4. 依次选择“身份验证”、“+ 添加身份验证方法”选项卡。  

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure3.png)

5. 选择“SAML Microsoft Office 365”作为身份验证方法。 

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure4.png)

6. 在“身份验证方法”  部分执行以下步骤：

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure5.png)

    a. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    b. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。  

    c. 单击“保存”  。

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

在本部分，我们将通过授予 Britta Simon 访问 N2F - Expense reports 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“N2F - Expense reports”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“N2F - Expense reports”。 

    ![应用程序列表中的 N2F - Expense reports 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-n2f---expense-reports-test-user"></a>创建 N2F - Expense reports 测试用户

若要让 Azure AD 用户登录 N2F - Expense reports，必须将其预配到 N2F - Expense reports。 对于 N2F - Expense reports 而言，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 N2F - Expense reports 公司站点。

2. 单击“设置”，然后从下拉列表中选择“高级设置”。  

    ![N2F - Expense - 添加用户](./media/n2f-expensereports-tutorial/configure1.png)

3. 在左侧导航面板中选择“用户”选项卡。 

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/user1.png)

4. 选择“+ 新建用户”选项卡。 

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/user2.png)

5. 在“用户”部分执行以下步骤： 

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/user3.png)

    a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 brittasimon\@contoso.com。

    b. 在“名字”文本框中，输入用户的名字（如“Britta”）   。

    c. 在“名称”文本框中输入用户的姓名，例如“BrittaSimon”。  

    d. 根据组织的要求选择“角色，直属经理(N + 1)”和“部门”。  

    e. 单击“验证并发送邀请”。 

    > [!NOTE]
    > 如果添加用户时遇到任何问题，请联系 [N2F - Expense reports 支持团队](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 N2F - Expense reports 磁贴时，应当会自动登录到你为其设置了 SSO 的 N2F - Expense reports。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

