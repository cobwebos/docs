---
title: 教程：Azure Active Directory 与 Elium 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Elium 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac14168f9f56a727517232e201e581f913d67c90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "65862843"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>教程：Azure Active Directory 与 Elium 集成

本教程介绍如何将 Elium 与 Azure Active Directory (Azure AD) 集成。
将 Elium 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Elium。
* 可让用户使用其 Azure AD 帐户自动登录到 Elium（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Elium 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Elium 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Elium 支持 **SP** 和 **IDP** 发起的 SSO

* Elium 支持**实时**用户预配

## <a name="adding-elium-from-the-gallery"></a>从库中添加 Elium

若要配置 Elium 与 Azure AD 的集成，需要从库中将 Elium 添加到托管 SaaS 应用列表。

**若要从库中添加 Elium，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Elium”，在结果面板中选择“Elium”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 Elium](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Elium 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Elium 相关用户之间建立链接关系。

若要配置和测试 Elium 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Elium 单一登录](#configure-elium-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Elium 测试用户](#create-elium-test-user)** - 在 Elium 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Elium 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Elium”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”图标，打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Elium 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/acs`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Elium 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE] 
    > 这些不是实际值。 从可以在 `https://<platform-domain>.elium.com/login/saml2/metadata` 中下载的 **SP 元数据文件**获取这些值。本教程稍后将对此做出说明。

6. Elium 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![image](common/edit-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤： 

    | 名称 | 源属性|
    | ---------------| ----------------|
    | 电子邮件   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > 这是默认声明。 **只需电子邮件声明**。 对于 JIT 预配，也只需电子邮件声明。 其他自定义声明根据客户平台的不同而异。

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 Elium”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-elium-single-sign-on"></a>配置 Elium 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Elium 公司站点。

2. 在右上角单击“用户配置文件”，然后选择“管理”。

    ![配置单一登录](./media/elium-tutorial/user1.png)

3. 选择“安全性”选项卡。

    ![配置单一登录](./media/elium-tutorial/user2.png)

4. 向下滚动到“单一登录(SSO)”部分中，并执行以下步骤：

    ![配置单一登录](./media/elium-tutorial/user3.png)

    a. 复制“验证 SAML2 身份验证是否适用于你的帐户”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。

    > [!NOTE]
    > 配置 SSO 后，始终可以通过以下 URL 访问默认的远程登录页：`https://<platform_domain>/login/regular/login` 

    b. 选中“启用 SAML2 联合身份验证”复选框。

    c. 选中“JIT 预配”复选框。

    d. 单击“下载”按钮打开“SP 元数据”。

    e. 在“SP 元数据”文件中搜索 **entityID**，复制“entityID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中。 

    ![配置单一登录](./media/elium-tutorial/user4.png)

    f. 在“SP 元数据”文件中搜索 **AssertionConsumerService**，复制“Location”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中。

    ![配置单一登录](./media/elium-tutorial/user5.png)

    g. 在写字板中打开从 Azure 门户下载的元数据文件内容，复制内容并将其粘贴到“IdP 元数据”文本框中。

    h. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 Elium 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Elium”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Elium”。

    ![应用程序列表中的 Elium 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中，从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-elium-test-user"></a>创建 Elium 测试用户

在本部分，我们将在 Elium 中创建名为 Britta Simon 的用户。 Elium 支持默认已启用的**实时预配**。 此部分不存在任何操作项。 尝试访问 Elium 时，如果 Elium 中尚不存在用户，则系统会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Elium 支持团队](mailto:support@elium.com)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Elium”磁贴时，应会自动登录到设置了 SSO 的 Elium。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

