---
title: 教程：Azure Active Directory 与 dmarcian 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 dmarcian 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4768c38b2e76bfa8cdff3187c32c03fdaaf57bbf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207647"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>教程：Azure Active Directory 与 dmarcian 集成

本教程介绍如何将 dmarcian 与 Azure Active Directory (Azure AD) 集成。
将 dmarcian 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 dmarcian。
* 可让用户使用其 Azure AD 帐户自动登录到 dmarcian（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 dmarcian 的集成，需做好以下准备：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 dmarcian 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* dmarcian 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-dmarcian-from-the-gallery"></a>从库中添加 dmarcian

若要配置 dmarcian 与 Azure AD 的集成，需要从库中将 dmarcian 添加到托管 SaaS 应用列表。

**若要从库中添加 dmarcian，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“dmarcian”，在结果面板中选择“dmarcian”，然后单击“添加”按钮添加应用程序。

     ![结果列表中的 dmarcian](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 dmarcian 配置和测试 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 dmarcian 相关用户之间建立链接关系。

若要配置并测试 dmarcian 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 dmarcian 单一登录](#configure-dmarcian-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 dmarcian 测试用户](#create-dmarcian-test-user)** - 在 dmarcian 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 dmarcian 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 **dmarcian** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![dmarcian 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![dmarcian 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新这些值。 

6. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>配置 dmarcian 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 dmarcian。

2. 单击右上角的“配置文件”，并导航到“首选项”。

    ![首选项 ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. 向下滚动并单击“单一登录”部分，然后单击“配置”。

    ![单人 ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. 在“SAML 单一登录”页上，将“状态”设置为“已启用”并执行以下步骤：

    ![身份验证 ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * 在“将 dmarcian 添加到标识提供者”部分，单击“复制”以复制实例的“断言使用者服务 URL”，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分的“回复 URL”文本框中。

    * 在“将 dmarcian 添加到标识提供者”部分，单击“复制”以复制实例的“实体 ID”，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分的“标识符”文本框中。

    * 在“设置身份验证”部分的“标识提供者元数据”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”。

    * 在“设置身份验证”部分的“属性语句”文本框中，粘贴 URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * 在“设置登录 URL”部分，复制实例的“登录 URL”，并将其粘贴到 Azure 门户上“基本的 SAML 配置”部分的“登录 URL”文本框中。

        > [!Note]
        > 可以根据组织的情况修改“登录 URL”。

    * 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 dmarcian 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“dmarcian”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“dmarcian”。

    ![应用程序列表中的 dmarcian 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-dmarcian-test-user"></a>创建 dmarcian 测试用户

要使 Azure AD 用户能够登录到 dmarcian，必须将其预配到 dmarcian 中。 在 dmarcian 中，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 dmarcian。

2. 单击右上角的“配置文件”，并导航到“管理用户”。

    ![用户 ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. 在“SSO 用户”部分的右侧，单击“添加新用户”。

    ![添加用户 ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. 在“添加新用户”弹出窗口中执行以下步骤：

    ![新用户 ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. 在“新用户的电子邮件”文本框中，输入用户的电子邮件地址，例如 **brittasimon@contoso.com**。

    b. 若要向该用户授予管理员权限，请选择“将用户设为管理员”。

    c. 单击“添加用户”。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 dmarcian 磁贴时，应当会自动登录到为其设置了 SSO 的 dmarcian。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

