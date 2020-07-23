---
title: 教程：Azure Active Directory 与 Pingboard 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Pingboard 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 341d8dd712b858572ec5df76b176258ca87c8857
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094426"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>教程：Azure Active Directory 与 Pingboard 集成

在本教程中，了解如何将 Pingboard 与 Azure Active Directory (Azure AD) 集成。
将 Pingboard 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Pingboard。
* 可以让用户使用其 Azure AD 帐户自动登录到 Pingboard（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Pingboard 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Pingboard 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Pingboard 支持 **SP** 和 **IDP** 发起的 SSO

* Pingboard 支持[自动用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>从库中添加 Pingboard

要配置 Pingboard 与 Azure AD 的集成，需要从库中将 Pingboard 添加到托管 SaaS 应用列表。

**若要从库中添加 Pingboard，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Pingboard”，在结果面板中选择“Pingboard”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Pingboard](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为“Britta Simon”的测试用户配置和测试 Pingboard 的 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 Pingboard 相关用户之间建立链接关系。

若要配置和测试 Pingboard 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Pingboard 单一登录](#configure-pingboard-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **创建 Pingboard 测试用户[ - 在 Pingboard 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式](#create-pingboard-test-user)** 。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Pingboard 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“Pingboard”应用程序集成页上，选择“单一登录”  。 

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![Pingboard 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入 URL：`http://app.pingboard.com/sp`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<entity-id>.pingboard.com/auth/saml/consume`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Pingboard 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > 这些不是实际值。 使用实际的回复 URL 和登录 URL 更新这些值。 请联系 [Pingboard 客户端支持团队](https://support.pingboard.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Pingboard”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-pingboard-single-sign-on"></a>配置 Pingboard 单一登录

1. 若要在 Pingboard 端配置 SSO，请打开新的浏览器窗口，并登录到 Pingboard 帐户。 必须是 Pingboard 管理员才能设置单一登录。

2. 从顶部菜单中选择“应用”>“集成” 

    ![配置单一登录](./media/pingboard-tutorial/Pingboard_integration.png)

3. 在“集成”  页上，找到“Azure Active Directory”  磁贴，并单击它。

    ![Pingboard 单一登录集成](./media/pingboard-tutorial/Pingboard_aad.png)

4. 在后面的模式中，单击“配置” 

    ![Pingboard 配置按钮](./media/pingboard-tutorial/Pingboard_configure.png)

5. 在以下页上，会出现“Azure SSO 集成已启用”。 在记事本中打开下载的元数据 XML 文件，并将其内容粘贴到 **IDP 元数据**。

    ![Pingboard SSO 配置界面](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. 验证文件后，如果所有内容都正确，则会立即启用单一登录。

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

本部分通过授予 Britta Simon 访问 Pingboard 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Pingboard”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Pingboard”  。

    ![应用程序列表中的 Pingboard 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-pingboard-test-user"></a>创建 Pingboard 测试用户

本部分的目的是在 Pingboard 中创建名为“Britta Simon”的用户。 Pingboard 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](pingboard-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤： 

1. 以管理员身份登录到 Pingboard 公司站点。

2. 单击“目录”页上的“添加员工”按钮。

    ![添加员工](./media/pingboard-tutorial/create_testuser_add.png)

3. 在“添加员工”对话框页上，执行以下步骤  ：

    ![邀请人员](./media/pingboard-tutorial/create_testuser_name.png)

    a. 在“全名”文本框中，键入用户的全名，如 Britta Simon   。

    b. 在“电子邮件”文本框中，键入用户的电子邮件地址，如 brittasimon@contoso.com。

    c. 在“职务”  文本框中，键入 Britta Simon 的职务。

    d. 在“位置”  下拉列表中，选择 Britta Simon 的位置。

    e. 单击“添加”  。

4. 确认界面随即出现，以便确认添加用户。

    ![确认](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Pingboard 磁贴时，应当会自动登录到你为其设置了 SSO 的 Pingboard。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
