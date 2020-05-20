---
title: 教程：Azure Active Directory 与 Trisotech Digital Enterprise Server 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Trisotech Digital Enterprise Server 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 56399f99ede611c4a120603cce3a3eede2728c6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088265"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>教程：Azure Active Directory 与 Trisotech Digital Enterprise Server 的集成

本教程介绍如何将 Trisotech Digital Enterprise Server 与 Azure Active Directory (Azure AD) 集成。
将 Trisotech Digital Enterprise Server 与 Azure AD 集成可获得以下优势：

* 可在 Azure AD 中控制谁有权访问 Trisotech Digital Enterprise Server。
* 可让用户使用其 Azure AD 帐户自动登录到 Trisotech Digital Enterprise Server（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Trisotech Digital Enterprise Server 的集成，需要做好以下准备：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Trisotech Digital Enterprise Server 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Trisotech Digital Enterprise Server 支持 **SP** 发起的 SSO

* Trisotech Digital Enterprise Server 支持**实时**用户预配

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>从库中添加 Trisotech Digital Enterprise Server

若要配置 Trisotech Digital Enterprise Server 与 Azure AD 的集成，需要从库中将 Trisotech Digital Enterprise Server 添加到托管 SaaS 应用列表。

**若要从库中添加 Trisotech Digital Enterprise Server，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Trisotech Digital Enterprise Server**，在结果面板中选择“Trisotech Digital Enterprise Server”，然后单击“添加”按钮添加该应用程序。  

     ![结果列表中的“Trisotech Digital Enterprise Server”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Trisotech Digital Enterprise Server 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Trisotech Digital Enterprise Server 相关用户之间建立链接关系。

若要配置并测试 Trisotech Digital Enterprise Server 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Trisotech Digital Enterprise Server 单一登录](#configure-trisotech-digital-enterprise-server-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Trisotech Digital Enterprise Server 测试用户](#create-trisotech-digital-enterprise-server-test-user)** - 在 Trisotech Digital Enterprise Server 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Trisotech Digital Enterprise Server 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Trisotech Digital Enterprise Server”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Trisotech Digital Enterprise Server 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.trisotech.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.trisotech.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Trisotech Digital Enterprise Server 客户端支持团队](mailto:support@trisotech.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>配置 Trisotech Digital Enterprise Server 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到“Trisotech Digital Enterprise Server 配置”公司站点。

2. 单击“菜单”图标并选择“管理”。  

    ![配置单一登录](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. 选择“用户提供程序”。 

    ![配置单一登录](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. 在“用户提供程序配置”部分执行以下步骤： 

    ![配置单一登录](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. 从“身份验证方法”中的下拉列表内选择“安全断言标记语言 2 (SAML 2)”。  

    b. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的**应用联合元数据 URL**值。

    c. 在“应用程序 ID”文本框中，使用以下模式输入 URL：`https://<companyname>.trisotech.com`。

    d. 单击“保存” 

    e. 在“允许的域(空白表示任何人)”文本框中输入域名。系统会自动分配与“允许的域”匹配的用户的许可证 

    f. 单击“保存” 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Trisotech Digital Enterprise Server 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Trisotech Digital Enterprise Server”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“Trisotech Digital Enterprise Server”。 

    ![应用程序列表中的“Trisotech Digital Enterprise Server”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>创建 Trisotech Digital Enterprise Server 测试用户

在本部分，我们将在 Trisotech Digital Enterprise Server 中创建名为 Britta Simon 的用户。 Trisotech Digital Enterprise Server 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Trisotech Digital Enterprise Server 中尚不存在用户，身份验证后会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Trisotech Digital Enterprise Server 支持团队](mailto:support@trisotech.com)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Trisotech Digital Enterprise Server”磁贴时，应会自动登录到设置了 SSO 的 Trisotech Digital Enterprise Server。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

