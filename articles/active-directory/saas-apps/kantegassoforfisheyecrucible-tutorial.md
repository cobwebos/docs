---
title: 教程：Azure Active Directory 与 Kantega SSO for FishEye/Crucible 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Kantega SSO for FishEye/Crucible 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f262f94c451d5dcffd933bdebb1374b8733b9fd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67595175"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>教程：Azure Active Directory 与 Kantega SSO for FishEye/Crucible 集成

本教程介绍如何将 Kantega SSO for FishEye/Crucible 与 Azure Active Directory (Azure AD) 集成。
将 Kantega SSO for FishEye/Crucible 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Kantega SSO for FishEye/Crucible。
* 可让用户通过其 Azure AD 帐户自动登录到 Kantega SSO for FishEye/Crucible（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Kantega SSO for FishEye/Crucible 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Kantega SSO for FishEye/Crucible 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Kantega SSO for FishEye/Crucible 支持启用了 **SP 和 IDP** 的 SSO

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>从库中添加 Kantega SSO for FishEye/Crucible

若要配置 Kantega SSO for FishEye/Crucible 的集成（集成到 Azure AD 中），需从库中将 Kantega SSO for FishEye/Crucible 添加到托管 SaaS 应用的列表中。

若要从库中添加 Kantega SSO for FishEye/Crucible，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Kantega SSO for FishEye/Crucible”，从结果面板中选择“Kantega SSO for FishEye/Crucible”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 Kantega SSO for FishEye/Crucible](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Kantega SSO for FishEye/Crucible 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Kantega SSO for FishEye/Crucible 中相关用户之间建立链接关系。

若要配置并测试 Kantega SSO for FishEye/Crucible 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Kantega SSO for FishEye/Crucible 单一登录](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **创建 Kantega SSO for FishEye/Crucible 测试用户[ - 在 Kantega SSO for FishEye/Crucible 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份](#create-kantega-sso-for-fisheyecrucible-test-user)** 。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要配置 Kantega SSO for FishEye/Crucible 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Kantega SSO for FishEye/Crucible”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![Kantega SSO for FishEye/Crucible 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Kantega SSO for FishEye/Crucible 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 在配置 FishEye/Crucible 插件的过程中，将接收这些值，这将在教程的后面部分进行说明。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Kantega SSO for FishEye/Crucible”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>配置 Kantega SSO for FishEye/Crucible 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 FishEye/Crucible 本地服务器。

1. 将鼠标悬停在小齿轮上，并单击“外接程序”  。

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. 在“系统设置”部分，单击“查找新外接程序”  。 

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. 搜索“Kantega SSO for Crucible”  ，然后单击“安装”  按钮安装新的 SAML 插件。

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. 插件安装随即开始。 

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. 安装完成后。 单击“关闭”  。

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  单击“管理”。 

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. 单击“配置”  配置新的插件。 

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. 在“SAML”部分中  。 从“添加标识提供者”下拉菜单选择“Azure Active Directory (Azure AD)”   。

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. 选择订阅级别为“基本”  。

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. 在“应用属性”部分，执行以下步骤  ：

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. 复制“应用 ID URI”值并将其用作 Azure 门户中“基本 SAML 配置”部分中的“标识符、回复 URL 和登录 URL”。

    b. 单击“下一步”。 

1. 在“元数据导入”部分，执行以下步骤  ：

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. 选择“我的计算机上的元数据文件”，上传从 Azure 门户下载的元数据文件  。

    b. 单击“下一步”。 

1. 在“名称和 SSO 位置”部分，执行以下步骤  ：

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. 在“标识提供者名称”文本框中添加标识提供者名称（例如 Azure AD）  。

    b. 单击“下一步”。 

1. 验证签名证书，然后单击“下一步”  。   

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. 在“FishEye 用户帐户”  部分，执行以下步骤：

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. 选择“根据需要在 FishEye 的内部目录中创建用户”  ，并输入用户的组的合适名称（可以为多个 组，用逗号隔开）。

    b. 单击“下一步”。 

1. 单击“完成”  。

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. 在“Azure AD 的已知域”部分，执行以下步骤  ：  

    ![配置单一登录](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. 从页的左侧面板中选择“已知域”  。

    b. 在“已知域”文本框中输入域名  。

    c. 单击“保存”  。

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

在本部分中，通过授予 Britta Simon 访问 Kantega SSO for FishEye/Crucible 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Kantega SSO for FishEye/Crucible”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Kantega SSO for FishEye/Crucible”  。

    ![应用程序列表中的 Kantega SSO for FishEye/Crucible 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>创建 Kantega SSO for FishEye/Crucible 测试用户

要使 Azure AD 用户能够登录 FishEye/Crucible，必须将这些用户预配到 FishEye/Crucible 中。 在 Kantega SSO for FishEye/Crucible 中，需手动完成预配任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Crucible 本地服务器。

1. 将鼠标悬停在小齿轮上，然后单击“用户”  。

    ![添加员工](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. 在“用户”  选项卡部分，单击“添加用户”  。

    ![添加员工](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. 在“添加新用户”  对话框页上，执行以下步骤：

    ![添加员工](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    b. 在“显示名称”  文本框中，键入用户的显示名称（如 Britta Simon）。

    c. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“密码”文本框中，键入用户的密码。 

    e. 在“确认密码”文本框中，再次输入用户密码  。

    f. 单击“添加”  。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Kantega SSO for FishEye/Crucible 磁贴时，应当会自动登录到为其设置了 SSO 的 Kantega SSO for FishEye/Crucible。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)