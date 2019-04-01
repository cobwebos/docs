---
title: 教程：Azure Active Directory 与 Jitbit Helpdesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Jitbit Helpdesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 71b7cfb92b4caded75e0723564a09fae9f10858c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359767"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>教程：Azure Active Directory 与 Jitbit Helpdesk 集成

在本教程中，了解如何将 Jitbit Helpdesk 与 Azure Active Directory (Azure AD) 集成。
将 Jitbit Helpdesk 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Jitbit Helpdesk。
* 可让用户使用其 Azure AD 帐户自动登录到 Jitbit Helpdesk（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Jitbit Helpdesk 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Jitbit Helpdesk 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Jitbit Helpdesk 支持 **SP** 发起的 SSO

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>从库中添加 Jitbit Helpdesk

若要配置 Jitbit Helpdesk 与 Azure AD 的集成，需要从库中将 Jitbit Helpdesk 添加到托管 SaaS 应用列表。

**若要从库添加 Jitbit Helpdesk，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Jitbit Helpdesk**，在结果面板中选择“Jitbit Helpdesk”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的“Jitbit Helpdesk”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Jitbit Helpdesk 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Jitbit Helpdesk 相关用户之间建立链接关系。

若要配置和测试 Jitbit Helpdesk 的 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Jitbit Helpdesk 单一登录](#configure-jitbit-helpdesk-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Jitbit Helpdesk 测试用户](#create-jitbit-helpdesk-test-user)** - 在 Jitbit Helpdesk 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Jitbit Helpdesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Jitbit Helpdesk”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![Jitbit Helpdesk 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。 请联系 [Jitbit Helpdesk 客户端支持团队](https://www.jitbit.com/support/)获取此值。

    b. 在“标识符(实体 ID)”文本框中，键入如下所示的 URL：`https://www.jitbit.com/web-helpdesk/`

5. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Jitbit Helpdesk”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>配置 Jitbit Helpdesk 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Jitbit Helpdesk 公司站点。

1. 在顶部工具栏中，单击“管理”。

    ![管理](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. 单击“常规设置”。

    ![用户、公司和权限](./media/jitbit-helpdesk-tutorial/ic777680.png "用户、公司和权限")

1. 在“身份验证设置”配置部分中，执行以下步骤：

    ![身份验证设置](./media/jitbit-helpdesk-tutorial/ic777683.png "身份验证设置")

    a. 选择“启用 SAML 2.0 单一登录”，通过“OneLogin”使用单一登录 (SSO) 进行登录。

    b. 在“终结点 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中

    d. 单击“保存更改”。

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

在本部分中，通过授予 Britta Simon 对 Jitbit Helpdesk 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Jitbit Helpdesk”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Jitbit Helpdesk”。

    ![“应用程序”列表中的“Jitbit Helpdesk”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-jitbit-helpdesk-test-user"></a>创建 Jitbit Helpdesk 测试用户

若要使 Azure AD 用户能够登录到 Jitbit Helpdesk，必须在 Jitbit Helpdesk 中预配这些用户。 对于 Jitbit Helpdesk，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Jitbit Helpdesk** 租户。

1. 在顶部菜单中，单击“管理”。

    ![管理](./media/jitbit-helpdesk-tutorial/ic777681.png "Administration")

1. 单击“用户、公司和权限”。

    ![用户、公司和权限](./media/jitbit-helpdesk-tutorial/ic777682.png "用户、公司和权限")

1. 单击“添加用户”。

    ![添加用户](./media/jitbit-helpdesk-tutorial/ic777685.png "添加用户")

1. 在“创建”部分中，键入要预配的 Azure AD 帐户，如下所示：

    ![创建](./media/jitbit-helpdesk-tutorial/ic777686.png "创建")

   a. 在“用户名”文本框中，键入类似于 **BrittaSimon** 的用户名。

   b. 在“电子邮件”文本框中，键入用户的电子邮件地址（如 BrittaSimon@contoso.com）。

   c. 在“名字”文本框中，键入用户的名字（如“Britta”）。

   d. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。

   e. 单击“创建”。

> [!NOTE]
> 可以使用任何其他 Jitbit Helpdesk 用户帐户创建工具或 Jitbit Helpdesk 提供的 API 来预配 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Jitbit Helpdesk”磁贴时，应会自动登录到设置了 SSO 的 Jitbit Helpdesk。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
