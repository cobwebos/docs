---
title: 教程：Azure Active Directory 与 Hornbill 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Hornbill 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f52259397acc04a5162aa31c9d3ef9dfb93c0ef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158056"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>教程：Azure Active Directory 与 Hornbill 的集成

在本教程中，了解如何将 Hornbill 与 Azure Active Directory (Azure AD) 集成。
将 Hornbill 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Hornbill。
* 可让用户使用其 Azure AD 帐户自动登录到 Hornbill（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Hornbill 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Hornbill 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Hornbill 支持 SP 发起的 SSO 
* Hornbill 支持实时用户预配 

## <a name="adding-hornbill-from-the-gallery"></a>从库中添加 Hornbill

要配置 Hornbill 与 Azure AD 的集成，需要从库中将 Hornbill 添加到托管 SaaS 应用列表。

**若要从库中添加 Hornbill，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Hornbill”，在结果面板中选择“Hornbill”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Hornbill](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Hornbill 的 Azure AD 单一登录  。
要正常使用单一登录，需在 Azure AD 用户与 Hornbill 相关用户之间建立链接关系。

若要配置并测试 Hornbill 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Hornbill 单一登录](#configure-hornbill-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Hornbill 测试用户](#create-hornbill-test-user)** - 在 Hornbill 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Hornbill 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户的 [Hornbill](https://portal.azure.com/) 应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”图标，打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Hornbill 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Hornbill 客户端支持团队](https://www.hornbill.com/support/?request/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-hornbill-single-sign-on"></a>配置 Hornbill 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Hornbill。

2. 在主页上单击“系统”  。

    ![Hornbill 系统](./media/hornbill-tutorial/tutorial_hornbill_system.png)

3. 导航到“安全性”。 

    ![Hornbill 安全性](./media/hornbill-tutorial/tutorial_hornbill_security.png)

4. 单击“SSO 配置文件”。 

    ![Hornbill 单一登录](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

5. 在页面右侧，单击“添加徽标”。 

    ![Hornbill 添加](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

6. 在“配置文件详细信息”栏上，单击“导入 SAML Meta 徽标”。  

    ![Hornbill 徽标](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

7. 在弹出页面上的“URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”，然后单击“处理”    。

    ![Hornbill 处理](./media/hornbill-tutorial/tutorial_hornbill_process.png)

8. 单击“处理”后，值将自动填充到“配置文件详细信息”部分下面。 

    ![Hornbill 页面 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill 页面 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Hornbill 页面 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

9. 单击 **“保存更改”** 。

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

在本部分中，通过向 Britta Simon 授予 Hornbill 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Hornbill”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Hornbill”  。

    ![应用程序列表中的 Hornbill 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”按钮。   

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-hornbill-test-user"></a>创建 Hornbill 测试用户

在本部分中，我们会在 Hornbill 中创建一个名为 Britta Simon 的用户。 Hornbill 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Hornbill 中不存在用户，则会在身份验证后创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系  [Hornbill 客户端支持团队](https://www.hornbill.com/support/?request/)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Hornbill 磁贴时，应当会自动登录到设置了 SSO 的 Hornbill。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

