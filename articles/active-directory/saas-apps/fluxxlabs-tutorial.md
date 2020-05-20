---
title: 教程：Azure Active Directory 与 Fluxx Labs 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Fluxx Labs 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102392"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>教程：Azure Active Directory 与 Fluxx Labs 集成

本教程介绍如何将 Fluxx Labs 与 Azure Active Directory (Azure AD) 集成。
将 Fluxx Labs 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Fluxx Labs。
* 可让用户使用其 Azure AD 帐户自动登录到 Fluxx Labs（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Fluxx Labs 的集成，需要做好以下各项准备：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Fluxx Labs 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Fluxx Labs 支持 **IDP** 发起的 SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>从库中添加 Fluxx Labs

要配置 Fluxx Labs 与 Azure AD 的集成，需要从库中将 Fluxx Labs 添加到托管 SaaS 应用列表。

**若要从库添加 Fluxx Labs，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Fluxx Labs”，在结果面板中选择“Fluxx Labs”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Fluxx Labs](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Fluxx Labs 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Fluxx Labs 相关用户之间建立链接关系。

若要配置并测试 Fluxx Labs 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Fluxx Labs 单一登录](#configure-fluxx-labs-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Fluxx Labs 测试用户](#create-fluxx-labs-test-user)** - 在 Fluxx Labs 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Fluxx Labs 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Fluxx Labs”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    ![Fluxx Labs 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL： 

    | 环境 | URL 模式|
    |-------------|------------|
    | 生产 | `https://<subdomain>.fluxx.io` |
    | 预生产 | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    | 环境 | URL 模式|
    |-------------|------------|
    | 生产 | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 预生产 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Fluxx Labs 客户端支持团队](mailto:travis@fluxxlabs.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Fluxx Labs”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-fluxx-labs-single-sign-on"></a>配置 Fluxx Labs 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Fluxx Labs 公司站点。

2. 在“设置”部分下，选择“管理员”。

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/config1.png)

3. 在管理面板中，选择“插件” > “集成”，然后选择“SAML SSO (已禁用)”

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/config2.png)

4. 在属性部分执行以下步骤：

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/config3.png)

    a. 选中“SAML SSO”复选框。 

    b. 在“请求路径”文本框中，键入 **/auth/saml**。

    c. 在“回调路径”文本框中，键入 **/auth/saml/callback**。

    d. 在“断言使用者服务 URL (单一登录 URL)”  文本框中，输入已在 Azure 门户中输入的“回复 URL”  值。

    e. 在“受众(SP 实体 ID)”  文本框中，输入已在 Azure 门户中输入的“标识符”  值。

    f. 在“标识提供者 SSO 目标 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    g. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”  文本框。

    h. 在“名称标识符格式”文本框中，输入值 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。

    i. 单击“保存”  。

    > [!NOTE]
    > 保存内容后，字段会出于安全原因显示空白，但值已保存到配置中。

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

在本部分，我们通过授予 Britta Simon 访问 Fluxx Labs 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Fluxx Labs”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Fluxx Labs”  。

    ![应用程序列表中的 Fluxx Labs 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-fluxx-labs-test-user"></a>创建 Fluxx Labs 测试用户

若要使 Azure AD 用户能够登录到 Fluxx Labs，必须将这些用户预配到 Fluxx Labs 中。 对于 Fluxx Labs，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Fluxx Labs 公司站点。

2. 单击下面显示的**图标**。

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/config6.png)

3. 在仪表板中，单击下面显示的图标打开“新建人员”卡。 

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/config4.png)

4. 在“新建人员”部分中，执行以下步骤： 

    ![Fluxx Labs 配置](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs 使用电子邮件作为 SSO 登录名的唯一标识符。 在“SSO UID”字段中填写用户的电子邮件地址，该值与他们进行 SSO 登录时使用的电子邮件地址匹配。 

    b. 单击“保存”  。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Fluxx Labs”磁贴时，应会自动登录到设置了 SSO 的 Fluxx Labs。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

