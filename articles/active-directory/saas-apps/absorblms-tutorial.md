---
title: 教程：Azure Active Directory 与 Absorb LMS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Absorb LMS 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936de76d1117c56f5a9dec48b51f33b9afa15351
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107507"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>教程：Azure Active Directory 与 Absorb LMS 的集成

本教程介绍如何将 Absorb LMS 与 Azure Active Directory (Azure AD) 集成。
将 Absorb LMS 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Absorb LMS。
* 可以让用户使用其 Azure AD 帐户自动登录到 Absorb LMS（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Absorb LMS 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Absorb LMS 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Absorb LMS 支持 IDP 发起的 SSO 

## <a name="adding-absorb-lms-from-the-gallery"></a>从库中添加 Absorb LMS

要配置 Absorb LMS 与 Azure AD 的集成，需要将库中的 Absorb LMS 添加到托管的 SaaS 应用列表。

**若要从库中添加 Absorb LMS，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Absorb LMS”，在结果面板中选择“Absorb LMS”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Absorb LMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们使用名为“Britta Simon”的测试用户配置和测试 Absorb LMS 的 Azure AD 单一登录  。
若要使单一登录有效，需要在 Azure AD 用户与 Absorb LMS 相关用户之间建立链接关系。

若要配置和测试 Absorb LMS 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Absorb LMS 单一登录](#configure-absorb-lms-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Absorb LMS 测试用户](#create-absorb-lms-test-user)** - 在 Absorb LMS 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Absorb LMS 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Absorb LMS 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”  页上，单击“编辑”  按钮，以打开“基本 SAML 配置”  对话框。

    ![Absorb LMS 域和 URL 单一登录信息](common/idp-intiated.png)

    如果使用的是 Absorb 5 - UI，请使用以下配置  ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://company.myabsorb.com/account/saml`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://company.myabsorb.com/account/saml`

    如果使用的是 Absorb 5 - New Learner Experience，请使用以下配置  ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://company.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Absorb LMS 客户端支持团队](https://support.absorblms.com/hc/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。

    ![image](common/edit-attribute.png)

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Absorb LMS”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-absorb-lms-single-sign-on"></a>配置 Absorb LMS 单一登录

1. 在新的 Web 浏览器窗口中，以管理员身份登录 Absorb LMS 公司站点。

2. 选择右上角的“帐户”  按钮。

    ![“帐户”按钮](./media/absorblms-tutorial/1.png)

3. 在“帐户”窗格中，选择“门户设置”  。

    ![“门户设置”链接](./media/absorblms-tutorial/2.png)

4. 选择“管理 SSO 设置”选项卡  。

    ![“用户”选项卡](./media/absorblms-tutorial/managesso.png)

5. 在“管理单一登录设置”页上，执行以下操作  ：

    ![“单一登录”配置页](./media/absorblms-tutorial/settings.png)

    a. 在“名称”文本框中输入名称，例如“Azure AD 市场 SSO”  。

    b. 选择“SAML”作为“方法”   。

    c. 在记事本中，打开从 Azure 门户下载的证书。 删除 ---BEGIN CERTIFICATE---  和 ---END CERTIFICATE---  标记。 然后，在“密钥”  框中，粘贴其余内容。

    d. 在“模式”  框中，选择“发起的标识提供者”  。

    e. 在“ID 属性”  框中，选择在 Azure AD 中配置为用户标识符的属性。 例如，如果在已 Azure AD 中选择“nameidentifier”，请选择“用户名”   。

    f. 选择“Sha256”作为“签名类型”   。

    g. 在“登录 URL”框中，粘贴 Azure 门户应用程序的“属性”页中的“用户访问 URL”。

    h. 在“注销 URL”中，粘贴从 Azure 门户的“配置登录”窗口中复制的“注销 URL”值。   

    i. 将“自动重定向”切换为“开启”   。

6. 选择“保存”  。

    ![“仅允许 SSO 登录”切换](./media/absorblms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 `brittasimon\@yourcompanydomain.extension`  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Absorb LMS 的权限，允许该用户使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Absorb LMS”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Absorb LMS”  。

    ![应用程序列表中的 Absorb LMS 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-absorb-lms-test-user"></a>创建 Absorb LMS 测试用户

要使 Azure AD 用户能够登录 Absorb LMS，必须在 Absorb LMS 中设置他们。 对于 Absorb LMS，预配是一项手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录 Absorb LMS 公司站点。

2. 在“用户”窗格中，选择“用户”   。

    ![“用户”链接](./media/absorblms-tutorial/absorblms_userssub.png)

3. 选择“用户”选项卡  。

    ![“新增”下拉列表](./media/absorblms-tutorial/absorblms_createuser.png)

4. 在“添加用户”页上执行以下操作： 

    ![“添加用户”页](./media/absorblms-tutorial/user.png)

    a. 在“名字”框中键入名字，例如 Britta。  

    b. 在“姓氏”框中键入姓氏，例如 Simon。  

    c. 在“用户名”框中键入全名，例如 Britta Simon。  

    d. 在“密码”框中，键入用户密码  。

    e. 在“确认密码”  框中，重新键入该密码。

    f. 将“是否活动”  设置切换为“活动”  。

5. 选择“保存”  。

    ![“仅允许 SSO 登录”切换](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 默认情况下，SSO 中未启用“用户预配”。 如果客户想启用此功能，需要按[此](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning)文档所述进行设置。 另请注意，只有采用 ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml` 的 Absorb 5 - New Learner Experience 才能使用“用户预配”功能

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Absorb LMS 磁贴时，应会自动登录到为其设置了 SSO 的 Absorb LMS。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
