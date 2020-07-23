---
title: 教程：Azure Active Directory 与 Riskware 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Riskware 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027117"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>教程：Azure Active Directory 与 Riskware 的集成

本教程介绍如何将 Riskware 与 Azure Active Directory (Azure AD) 集成。
将 Riskware 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Riskware。
* 可让用户使用其 Azure AD 帐户自动登录到 Riskware（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Riskware 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了 Riskware 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Riskware 支持 SP 发起的 SSO 

## <a name="adding-riskware-from-the-gallery"></a>从库中添加 Riskware

要配置 Riskware 与 Azure AD 的集成，需要从库中将 Riskware 添加到托管 SaaS 应用列表。

**若要从库中添加 Riskware，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Riskware”，在结果面板中选择“Riskware”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 Riskware](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Riskware 配置和测试 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Riskware 相关用户之间建立链接关系。

若要配置和测试 Riskware 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Riskware 单一登录](#configure-riskware-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Riskware 测试用户](#create-riskware-test-user)** - 在 Riskware 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Riskware 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在“Riskware”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Riskware 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL  ：
    
    | 环境| URL 模式|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. 在“标识符(实体 ID)”文本框中，键入 URL： 
    
    | 环境| URL 模式|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新此值。 若要获取此值，请与 [Riskware 客户端支持团队](mailto:support@pansoftware.com.au)联系。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Riskware”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-riskware-single-sign-on"></a>配置 Riskware 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Riskware 公司站点。

1. 在右上角，单击“维护”打开维护页。 

    ![Riskware 配置 - 维护](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. 在维护页中，单击“身份验证”。 

    ![Riskware 配置 - 身份验证](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. 在“身份验证配置”  页中执行以下步骤：

    ![Riskware 配置 - 身份验证配置](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. 选择“SAML”作为身份验证的**类型**。 

    b. 在“代码”文本框中，键入类似于 AZURE_UAT 的代码。 

    c. 在“说明”文本框中，键入类似于“AZURE Configuration for SSO”的说明。 

    d. 在“单一登录页”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    e. 在“注销页”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    f. 在“发布表单字段”  文本框中，键入“发布响应”中包含 SAML 的字段名称（如 SAMLResponse）

    g. 在“XML 标识标记名称”文本框中键入属性，其中包含 SAML 响应中的唯一标识符（如 NameID）。 

    h. 在记事本中打开从 Azure 门户下载的**元数据 XML**，复制元数据文件中的证书，并将其粘贴到“证书”文本框中 

    i. 在“使用者 URL”文本框中，粘贴从支持团队获取的“回复 URL”值。  

    j. 在“颁发者”文本框中，粘贴从支持团队获取的“标识符”值。  

    > [!Note]
    > 若要获取这些值，请与 [Riskware 客户端支持团队](mailto:support@pansoftware.com.au)联系。

    k. 选中“使用 POST”复选框。 

    l. 选中“使用 SAML 请求”  复选框。

    m. 单击“保存”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 `brittasimon@yourcompanydomain.extension`  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 Riskware 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Riskware”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Riskware”  。

    ![应用程序列表中的 Riskware 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-riskware-test-user"></a>创建 Riskware 测试用户

要使 Azure AD 用户能够登录到 Riskware，必须将其预配到 Riskware 中。 在 Riskware 中，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Riskware。

1. 在右上角，单击“维护”打开维护页。  

    ![Riskware 配置 - 维护](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. 在维护页中，单击“人员”。 

    ![Riskware 配置 - 人员](./media/riskware-tutorial/tutorial_riskware_people.png)

1. 选择“详细信息”  选项卡，并执行以下步骤：

    ![Riskware 配置 - 详细信息](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. 选择“人员类型”，例如“员工”。 

    b. 在“名字”文本框中，输入用户的名字（如“Britta”）   。

    c. 在“姓氏”文本框中，输入用户的姓氏（如“Simon”）   。

1. 在“安全”选项卡中，执行以下步骤： 

    ![Riskware 配置 - 安全性](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. 在“身份验证”部分下，选择设置的“身份验证”模式（例如“AZURE Configuration for SSO”）。  

    b. 在“登录详细信息”部分下的“用户 ID”文本框中，输入类似于 `brittasimon@contoso.com` 的用户电子邮件。

    c. 在“密码”文本框中，输入用户的密码。 

1. 在“组织”选项卡中执行以下步骤： 

    ![Riskware 配置 - 组织](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. 选择作为 **Level1** 组织的选项。

    b. 在“人员的主要工作区”部分下的“位置”文本框中，键入自己的位置。  

    c. 在“员工”部分下，选择“员工状态”，例如“休假”。  

    d. 单击“保存”  。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Riskware 磁贴时，应当会自动登录到为其设置了 SSO 的 Riskware。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
