---
title: 教程：Azure Active Directory 与 InstaVR Viewer 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 InstaVR Viewer 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19039a6a1337c2ddc7494805b18f564466d4f1f8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168725"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>教程：Azure Active Directory 与 InstaVR Viewer 的集成

本教程介绍如何将 InstaVR Viewer 与 Azure Active Directory (Azure AD) 相集成。
将 InstaVR Viewer 与 Azure AD 集成可带来以下优势：

* 可在 Azure AD 中控制谁有权访问 InstaVR Viewer。
* 可让用户使用其 Azure AD 帐户自动登录到 InstaVR Viewer（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 InstaVR Viewer 的集成，需准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 InstaVR Viewer 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* InstaVR Viewer 支持 **SP** 发起的 SSO
* InstaVR Viewer 支持**实时**用户预配。

## <a name="adding-instavr-viewer-from-the-gallery"></a>从库中添加 InstaVR Viewer

若要配置 InstaVR Viewer 与 Azure AD 的集成，需要从库中将 InstaVR Viewer 添加到托管 SaaS 应用列表。

**若要从库中添加 InstaVR Viewer，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **InstaVR Viewer**，在结果面板中选择“InstaVR Viewer”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 InstaVR Viewer](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为 **Britta Simon** 的测试用户使用 InstaVR Viewer 配置和测试 Azure AD 单一登录。
若要正常进行单一登录，需要在 Azure AD 用户与 InstaVR Viewer 相关用户之间建立链接关系。

若要配置和测试 InstaVR Viewer 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 InstaVR Viewer 单一登录](#configure-instavr-viewer-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 InstaVR Viewer 测试用户](#create-instavr-viewer-test-user)** - 在 InstaVR Viewer 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 InstaVR Viewer 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“InstaVR Viewer”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![InstaVR Viewer 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > “登录 URL”没有固定的模式。 它是在 InstaVR Viewer 客户执行 Web 打包时生成的。 此 URL 对于每个客户和包都是唯一的。 若要获取确切的登录 URL，需要登录到 InstaVR Viewer 实例并执行 Web 打包。

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > 标识符非实际值。 本教程稍后将介绍如何使用实际标识符值来更新此值。

5. 在“使用 SAML 设置单一登录”页上的“SAML 签名证书”部分，单击“下载”以根据要求通过给定的选项下载“证书(Base64)”和“联合元数据文件”，并将其保存在计算机上。

    ![证书下载链接](common/metadata-certificatebase64.png)

6. 在“设置 InstaVR Viewer”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-instavr-viewer-single-sign-on"></a>配置 InstaVR Viewer 单一登录

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 InstaVR Viewer 公司站点。

2. 单击**用户图标**并选择“帐户”。

    ![InstaVR Viewer 配置 ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. 向下滚动到“SAML 身份验证”并执行以下步骤：

    ![InstaVR Viewer 配置 ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. 在“SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    b. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    c. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    d. 若要上传所下载的证书文件，请单击“更新”。

    e. 若要上传所下载的联合元数据文件，请单击“更新”。

    f. 复制“实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。

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

在本部分，我们通过授予 Britta Simon 访问 InstaVR Viewer 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“InstaVR Viewer”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入 **InstaVR Viewer** 并将其选中。

    ![应用程序列表中的“InstaVR Viewer”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-instavr-viewer-test-user"></a>创建 InstaVR Viewer 测试用户

在本部分，我们将在 InstaVR Viewer 中创建名为 Britta Simon 的用户。 InstaVR Viewer 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 InstaVR Viewer 中不存在用户，则会在身份验证后创建一个新用户。 如果遇到任何问题，请联系 [InstaVR Viewer 支持团队](mailto:contact@instavr.co)。

### <a name="test-single-sign-on"></a>测试单一登录

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 InstaVR Viewer 公司站点。

2. 在左侧导航面板中选择“打包”，然后选择“创建 Web 包”。

    ![InstaVR Viewer 配置 ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. 选择“下载”。

    ![InstaVR Viewer 配置 ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. 选择“打开托管页面”，然后会重定向到 Azure AD 进行登录。

    ![InstaVR Viewer 配置 ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. 输入 Azure AD 凭据可通过 SSO 成功登录到 Azure AD。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
