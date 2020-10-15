---
title: 教程：Azure Active Directory 与 Citrix ShareFile 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Citrix ShareFile 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/18/2020
ms.author: jeedes
ms.openlocfilehash: cfbb704799a1884c689bd0de547526a33f1ba7ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88651860"
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
* 配置 Citrix ShareFile 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-citrix-sharefile-from-the-gallery"></a>从库添加 Citrix ShareFile

要配置 Citrix ShareFile 与 Azure AD 的集成，需要从库中将 Citrix ShareFile 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Citrix ShareFile” 。
1. 从结果面板中选择“Citrix ShareFile”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

在本部分中，基于一个名为 Britta Simon 的测试用户配置和测试 Citrix ShareFile 的 Azure AD 单一登录****。
若要运行单一登录，需要在 Azure AD 用户与 Citrix ShareFile 相关用户之间建立链接关系。

若要配置并测试 Citrix ShareFile 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. [配置 Citrix ShareFile SSO](#configure-citrix-sharefile-sso) - 在应用程序端配置单一登录设置。
    * **[创建 Citrix ShareFile 测试用户](#create-citrix-sharefile-test-user)** - 在 Citrix ShareFile 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Citrix ShareFile”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.sharefile.com/saml/login`。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL****：

    - `https://<tenant-name>.sharefile.com`
    - `https://<tenant-name>.sharefile.com/saml/info`
    - `https://<tenant-name>.sharefile1.com/saml/info`
    - `https://<tenant-name>.sharefile1.eu/saml/info`
    - `https://<tenant-name>.sharefile.eu/saml/info`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：
    
    - `https://<tenant-name>.sharefile.com/saml/acs`
    - `https://<tenant-name>.sharefile.eu/saml/<URL path>`
    - `https://<tenant-name>.sharefile.com/saml/<URL path>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Citrix ShareFile 客户端支持团队](https://www.citrix.co.in/products/citrix-content-collaboration/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Citrix ShareFile”部分，根据要求复制相应 URL****。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Citrix ShareFile 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Citrix ShareFile”。****
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-citrix-sharefile-sso"></a>配置 Citrix ShareFile SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到你的 **Citrix ShareFile** 公司站点。

1. 在左侧菜单中，单击“设置”图标 -> “安全性” -> “登录和安全策略”  。
   
    ![帐户管理](./media/sharefile-tutorial/settings-security.png "帐户管理")

1. 在“基本设置”**** 下的“单一登录/SAML 2.0 配置”**** 对话框页上，执行以下步骤：
   
    ![单一登录](./media/sharefile-tutorial/saml-configuration.png "单一登录")
   
    a. 在“启用 SAML”中选择“是” 。

    b. 复制“ShareFile 证书颁发者/实体 ID”值，将其粘贴到 Azure 门户的“基本 SAML 配置”对话框中的“标识符 URL”框中。  
    
    c. 在“IDP 证书颁发者/实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值********。

    d. 单击“X.509 证书”字段旁边的“更改”，并上传从 Azure 门户下载的证书********。
    
    e. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。
    
    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值********。

5. 在 Citrix ShareFile 管理门户中，单击 **“保存”**。

## <a name="create-citrix-sharefile-test-user"></a>创建 Citrix ShareFile 测试用户

1. 登录到 **Citrix ShareFile** 租户。

2. 单击“人员” -> “管理用户主页” -> “创建新用户” -> “创建员工”   。
   
    ![创建员工](./media/sharefile-tutorial/create-user.png "创建员工")

3. 在“基本信息”部分中执行以下步骤：****
   
    ![基本信息](./media/sharefile-tutorial/user-form.png "基本信息")
   
    a. 在“名字”文本框中，输入用户的名字，如 Britta************。
   
    b.  在“姓氏”文本框中，输入用户的姓氏，如 Simon************。
   
    c. 在“电子邮件地址”文本框中，键入 Britta Simon 的电子邮件地址为“brittasimon\@contoso.com”********。

4. 单击“添加用户”  。
  
    >[!NOTE]
    >Azure AD 帐户持有人将收到一封电子邮件，并在帐户激活前按照链接确认其帐户。可使用 Citrix ShareFile 提供的任何其他 Citrix ShareFile 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Citrix ShareFile 磁贴时，应当会自动登录到你为其设置了 SSO 的 Citrix ShareFile。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

