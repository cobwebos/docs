---
title: 教程：Azure Active Directory 与 Citrix ShareFile 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Citrix ShareFile 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160032"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>教程：Azure Active Directory 与 Citrix ShareFile 的集成

本教程介绍如何将 Citrix ShareFile 与 Azure Active Directory (Azure AD) 集成。
将 Citrix ShareFile 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Citrix ShareFile。
* 可以让用户使用其 Azure AD 帐户自动登录到 Citrix ShareFile（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Citrix ShareFile 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Citrix ShareFile 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Citrix ShareFile 支持 SP 发起的 SSO 

## <a name="adding-citrix-sharefile-from-the-gallery"></a>从库添加 Citrix ShareFile

要配置 Citrix ShareFile 与 Azure AD 的集成，需要从库中将 Citrix ShareFile 添加到托管 SaaS 应用列表。

**若要从库中添加 Citrix ShareFile，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Citrix ShareFile”，在结果面板中选择“Citrix ShareFile”，并单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Citrix ShareFile](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为 Britta Simon 的测试用户配置和测试 Citrix ShareFile 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Citrix ShareFile 相关用户之间建立链接关系。

若要配置并测试 Citrix ShareFile 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Citrix ShareFile 单一登录](#configure-citrix-sharefile-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Citrix ShareFile 测试用户](#create-citrix-sharefile-test-user)** - 在 Citrix ShareFile 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Citrix ShareFile 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“Citrix ShareFile”应用程序集成页上，单击“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Citrix ShareFile 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<tenant-name>.sharefile.com/saml/login`。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL  ：

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Citrix ShareFile 客户端支持团队](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Citrix ShareFile”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-citrix-sharefile-single-sign-on"></a>配置 Citrix ShareFile 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **Citrix ShareFile** 公司站点。

2. 在顶部工具栏中，单击“管理”。 

3. 在左侧导航窗格中，选择“配置单一登录”。 
   
    ![帐户管理](./media/sharefile-tutorial/ic773627.png "帐户管理")

4. 在“单一登录/ SAML 2.0 配置”  对话框页上，在“基本设置”  下执行以下步骤：
   
    ![单一登录](./media/sharefile-tutorial/ic773628.png "单一登录")
   
    a. 单击“启用 SAML”。 
    
    b. 在“IDP 证书颁发者/实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 单击“X.509 证书”字段旁边的“更改”，并上传从 Azure 门户下载的证书   。
    
    d. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
    
    e. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

5. 在 Citrix ShareFile 管理门户中单击“保存”。 

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

在本部分中，将通过向 Britta Simon 授予 Citrix ShareFile 的访问权限使之能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Citrix ShareFile”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Citrix ShareFile”  。

    ![应用程序列表中的 Citrix ShareFile 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-citrix-sharefile-test-user"></a>创建 Citrix ShareFile 测试用户

为了使 Azure AD 用户能够登录到 Citrix ShareFile，必须将其预配到 Citrix ShareFile 中。 对于 Citrix ShareFile，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Citrix ShareFile** 租户。

2. 单击“管理用户”\>“管理用户主页”\>“+ 创建员工”。
   
    ![创建员工](./media/sharefile-tutorial/IC781050.png "创建员工")

3. 在“基本信息”部分中执行以下步骤： 
   
    ![基本信息](./media/sharefile-tutorial/IC799951.png "基本信息")
   
    a. 在“电子邮件地址”文本框中，键入 Britta Simon 的电子邮件地址为“brittasimon\@contoso.com”。
   
    b. 在“名字”文本框中，输入用户的名字，如 Britta    。
   
    c. 在“姓氏”文本框中，输入用户的姓氏，如 Simon    。

4. 单击“添加用户”  。
  
    >[!NOTE]
    >Azure AD 帐户持有人将收到一封电子邮件，并在帐户激活前按照链接确认其帐户。可使用 Citrix ShareFile 提供的任何其他 Citrix ShareFile 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Citrix ShareFile 磁贴时，应当会自动登录到你为其设置了 SSO 的 Citrix ShareFile。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

