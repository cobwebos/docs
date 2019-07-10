---
title: 教程：Azure Active Directory 与 Spotinst 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Spotinst 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b3c9a0152144ac0d705c7d3f12c18cbc38398f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090150"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>教程：Azure Active Directory 与 Spotinst 集成

本教程将介绍如何将 Spotinst 与 Azure Active Directory (Azure AD) 集成。
Spotinst 与 Azure AD 的集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Spotinst。
* 可让用户使用其 Azure AD 帐户自动登录到 Spotinst（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Spotinst 的集成，需具备以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Spotinst 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Spotinst 支持启用了 **SP 和 IDP** 的 SSO

## <a name="adding-spotinst-from-the-gallery"></a>从库中添加 Spotinst

要配置 Spotinst 到 Azure AD 的集成，需要将库中的 Spotinst 添加到托管的 SaaS 应用的列表。

要从库中添加 Spotinst，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Spotinst”，在结果面板中选择“Spotinst”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 Spotinst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 Spotinst 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Spotinst 相关用户之间建立链接关系。

要配置和测试 Spotinst 的 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Spotinst 单一登录](#configure-spotinst-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Spotinst 测试用户](#create-spotinst-test-user)** - 在 Spotinst 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要配置 Spotinst 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“Spotinst”应用程序集成页面上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    ![Spotinst 域和 URL 单一登录信息](common/idp-preintegrated-relay.png)

    a. 选中“设置其他 URL”  。

    b. 在“中继状态”  文本框中，键入值：`<ID>`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![Spotinst 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 URL：  `https://console.spotinst.com`

    > [!NOTE]
    > “中继状态”值不是实际值。 用户将使用实际的中继状态值更新该值（详见本教程的稍后部分）。

6. Spotinst 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ： 

    | 名称 | 源属性|
    | -----| --------------- |
    | 电子邮件 | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 Spotinst”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-spotinst-single-sign-on"></a>配置 Spotinst 单一登录

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Spotinst。

2. 单击屏幕右上方的用户图标，再单击“设置”   。

    ![Spotinst 设置](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. 单击顶部的“安全性”选项卡，然后选择“标识提供者”并执行以下步骤   ：

    ![Spotinst 安全性](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. 复制实例的“中继状态”值并将其粘贴到 Azure 门户中“基本 SAML 配置”部分的“中继状态”文本框    。

    b. 单击“浏览”，上传从 Azure 门户下载的元数据 xml 文件 

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
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Spotinst 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Spotinst”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Spotinst”  。

    ![应用程序列表中的 Spotinst 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-spotinst-test-user"></a>创建 Spotinst 测试用户

本部分旨在 Spotinst 中创建名为 Britta Simon 的用户。

1. 如果已在启用了 SP 的模式下配置应用程序，请执行以下步骤  ：

   a. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Spotinst。

   b. 单击屏幕右上方的用户图标，再单击“设置”   。

    ![Spotinst 设置](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. 单击“用户”，然后选择“添加用户”   。

    ![Spotinst 设置](./media/spotinst-tutorial/adduser1.png)

    d. 在“添加用户”部分中，执行以下步骤：

    ![Spotinst 设置](./media/spotinst-tutorial/adduser2.png)

    * 在“全名”文本框中输入用户的全名，例如 BrittaSimon   。

    * 在“电子邮件”文本框中，输入用户的电子邮件地址（如 `brittasimon\@contoso.com`）  。

    * 为组织角色、帐户角色和帐户选择组织特定的详细信息  。

2. 如果已在启用了 IDP 的模式下配置应用程序，则此部分中没有操作项  。 Spotinst 支持实时预配（该预配默认启用）。 尝试访问 Spotinst 期间，如果尚不存在用户，则会创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Spotinst 磁贴时，应会自动登录到为其设置了 SSO 的 Spotinst。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

