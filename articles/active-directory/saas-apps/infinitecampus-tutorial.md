---
title: 教程：Azure Active Directory 与 Infinite Campus 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Infinite Campus 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100368"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>教程：Azure Active Directory 与 Infinite Campus 的集成

本教程介绍如何将 Infinite Campus 与 Azure Active Directory (Azure AD) 集成。
将 Infinite Campus 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Infinite Campus。
* 可让用户使用其 Azure AD 帐户自动登录到 Infinite Campus（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Infinite Campus 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Infinite Campus 单一登录的订阅
* 若要完成配置，你至少需要是 Azure Active Directory 管理员，并充当“学生信息系统 (SIS)”的校园产品安全角色。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Infinite Campus 支持 **SP** 发起的 SSO

## <a name="adding-infinite-campus-from-the-gallery"></a>从库中添加 Infinite Campus

若要配置 Infinite Campus 与 Azure AD 的集成，需要从库中将 Infinite Campus 添加到托管 SaaS 应用列表。

**若要从库添加 Infinite Campus，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮  。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Infinite Campus**，在结果面板中选择“Infinite Campus”，然后单击“添加”按钮添加该应用程序。  

    ![结果列表中的 Infinite Campus](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Infinite Campus 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Infinite Campus 相关用户之间建立链接关系。

若要配置并测试 Infinite Campus 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Infinite Campus 单一登录](#configure-infinite-campus-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Infinite Campus 测试用户](#create-infinite-campus-test-user)** - 在 Infinite Campus 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Infinite Campus 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Infinite Campus”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分执行以下步骤（请注意，域根据托管模型而异，但“完全限定的域”值必须与 Infinite Campus 安装相匹配）： 

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL： `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinite Campus 域和 URL 单一登录信息](common/sp-identifier-reply.png)

5. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>配置 Infinite Campus 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Infinite Campus。

2. 在菜单左侧，单击“系统管理”。 

    ![管理](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. 导航到“用户安全性” > “SAML 管理” > “SSO 服务提供程序配置”。

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. 在“SSO 服务提供程序配置”页上执行以下步骤： 

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. 选择“启用 SAML 单一登录”  。

    b. 编辑“可选属性名称”以包含“名称”  

    c. 在“选择检索标识提供者(IDP)服务器数据的选项”部分，选择“元数据 URL”，在框中粘贴从 Azure 门户复制的“应用联合元数据 URL”值，然后单击“同步”     。

    d. 单击“同步”后，将在“SSO 服务提供程序配置”页中自动填充值。   可以验证这些值是否与上述步骤 4 中显示的值相匹配。

    e. 单击“保存”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如，BrittaSimon@contoso.com 。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

> [!NOTE]
> 如果你希望所有 Azure 用户都能够对 Infinite Campus 进行单一登录访问，并依赖于使用 Infinite Campus 内部权限系统来控制访问，可将应用程序的“需要用户分配”属性设置为“否”，并跳过以下步骤。 

在本部分，我们通过授予 Britta Simon 访问 Infinite Campus 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Infinite Campus”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Infinite Campus”  。

    ![“应用程序”列表中的“Infinite Campus”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-infinite-campus-test-user"></a>创建 Infinite Campus 测试用户

Infinite Campus 采用以人口统计为中心的体系结构。 请联系 [Infinite Campus 支持团队](mailto:sales@infinitecampus.com)，以便在 Infinite Campus 平台中添加用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Infinite Campus”磁贴时，应会自动登录到设置了 SSO 的 Infinite Campus。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
