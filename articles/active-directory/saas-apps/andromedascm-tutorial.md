---
title: 教程：Azure Active Directory 与 Andromeda 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Andromeda 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68fa570ecfbafe2000bfa6eb9fa159dff48219a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107076"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>教程：Azure Active Directory 与 Andromeda 集成

在本教程中，了解如何将 Andromeda 与 Azure Active Directory (Azure AD) 集成。
将 Andromeda 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Andromeda。
* 可以让用户使用其 Azure AD 帐户自动登录到 Andromeda（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Andromeda 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了单一登录的 Andromeda 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Andromeda 支持 **SP 和 IDP** 发起的 SSO
* Andromeda 支持**实时**用户预配

## <a name="adding-andromeda-from-the-gallery"></a>从库中添加 Andromeda

若要配置 Andromeda 与 Azure AD 的集成，需要从库中将 Andromeda 添加到托管的 SaaS 应用列表。

**若要从库中添加 Andromeda，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Andromeda”，在结果面板中选择“Andromeda”，然后单击“添加”按钮添加该应用程序。   

    ![结果列表中的 Andromeda](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 Andromeda 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Andromeda 中相关用户之间建立链接关系。

若要配置和测试 Andromeda 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Andromeda 单一登录](#configure-andromeda-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Andromeda 测试用户](#create-andromeda-test-user)** - 在 Andromeda 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Andromeda 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Andromeda** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![Andromeda 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<tenantURL>.ngcxpress.com/`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Andromeda 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新该值。

6. Andromeda 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

    > [!Important]
    > 设置时清除命名空间定义。

7. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ： 

    | 名称 | 源属性|
    | ------ | -----------|
    | 角色 (role)        | 应用特定的角色 |
    | type        | 应用类型 |
    | company       | CompanyName |

    > [!NOTE]
    > 这些不是实际值。 这些值仅用于演示，请使用组织角色。

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“保存”  。

8. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

9. 在“设置 Andromeda”  部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-andromeda-single-sign-on"></a>配置 Andromeda 单一登录

1. 以管理员身份登录 Andromeda 公司站点。

2. 在菜单栏顶部单击“管理”并导航到“管理”。  

    ![Andromeda 管理员](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. 在工具栏左侧的“界面”部分下面，单击“SAML 配置”。  

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. 在“SAML 配置”  部分的页面上，执行以下步骤：

    ![Andromeda 配置](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. 选中“使用 SAML 启用 SSO”。 

    b. 在“Andromeda 信息”部分下，复制“SP 标识”值并将其粘贴到“基本 SAML 配置”部分的“标识符”文本框中。    

    c. 复制“使用者 URL”值，并将其粘贴到“基本 SAML 配置”部分的“回复 URL”文本框中。   

    d. 复制“登录 URL”值，并将其粘贴到“基本 SAML 配置”部分的“登录 URL”文本框中。   

    e. 在“SAML 标识提供者”部分下，键入 IDP 名称。 

    f. 在“单一登录终结点”  文本框中，粘贴从 Azure 门户复制的“登录 URL”  值。

    g. 在记事本中打开从 Azure 门户下载的 **Base64 编码证书**，将其粘贴到“X 509 证书”文本框中。 
    
    h. 将以下属性映射到相应的值，以便于从 Azure AD 进行 SSO 登录。 登录时必须使用“用户 ID”属性  。 预配时，必须使用“电子邮件”、“公司”、“用户类型”和“角色”     。 本部分定义要关联到 Azure 门户中定义的值的属性映射（名称和值）

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. 单击“保存”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Andromeda 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Andromeda”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Andromeda”  。

    ![应用程序列表中的 Andromeda 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-andromeda-test-user"></a>创建 Andromeda 测试用户

在本部分中，我们会在 Andromeda 中创建一个名为 Britta Simon 的用户。 Andromeda 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Andromeda 中尚不存在用户，则会在进行身份验证后创建一个新用户。 如果需要手动创建用户，请联系 [Andromeda 客户端支持团队](https://www.ngcsoftware.com/support/)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Andromeda 磁贴时，应会自动登录到你为其设置了 SSO 的 Andromeda。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)