---
title: 教程：Azure Active Directory 与 SignalFx 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SignalFx 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 2ce766da0521b787edec020d7dfc3de2a2d83b19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090706"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>教程：Azure Active Directory 与 SignalFx 集成

本教程介绍了如何将 SignalFx 与 Azure Active Directory (Azure AD) 进行集成。
将 SignalFx 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 SignalFx。
* 可让用户使用其 Azure AD 帐户自动登录到 SignalFx（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SignalFx 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 SignalFx 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SignalFx 支持 **IDP** 发起的 SSO
* SignalFx 支持**实时**用户预配

## <a name="adding-signalfx-from-the-gallery"></a>从库中添加 SignalFx

若要配置 SignalFx 与 Azure AD 的集成，需要从库中将 SignalFx 添加到托管 SaaS 应用列表。

**若要从库中添加 SignalFx，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“SignalFx”，在结果面板中选择“SignalFx”，然后单击“添加”按钮以添加该应用程序。   

     ![结果列表中的 SignalFx](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 SignalFx 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 SignalFx 相关用户之间建立链接关系。

若要配置和测试 SignalFx 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SignalFx 单一登录](#configure-signalfx-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SignalFx 测试用户](#create-signalfx-test-user)** - 在 SignalFx 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SignalFx 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“SignalFx”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    ![SignalFx 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入 URL：`https://api.signalfx.com/v1/saml/metadata` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > 上面的值不是实际值。 需要使用实际的回复 URL 更新该值（本教程稍后将会介绍）。

5. SignalFx 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ： 

    | 名称 |  源属性|
    | ------------------- | -------------------- |
    | User.FirstName     | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

7. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 SignalFx”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-signalfx-single-sign-on"></a>配置 SignalFx 单一登录

1. 以管理员身份登录到 SignalFx 公司站点。

1. 在 SignalFx 中，在顶部单击“集成”  以打开“集成”页。

    ![SignalFx 集成](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. 在“登录服务”部分下单击 **Azure Active Directory** 磁贴。 

    ![SignalFx SAML](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. 单击“新建集成”  并在“安装”  选项卡下执行以下步骤：

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. 在“名称”  文本框中，键入一个新的集成名称，例如 **OurOrgName SAML SSO**。

    b. 复制“集成 ID”值，并将其追加到“回复 URL”，取代 Azure 门户中“基本 SAML 配置”部分的“回复 URL”文本中的 `<integration ID>`。    

    c. 在“证书”文本框中，单击“上传文件”  以上传从 Azure 门户下载的 **Base64 编码证书**。 

    d. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    e. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。

    f. 单击“ **保存**”。

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

在本部分中，通过向 Britta Simon 授予对 SignalFx 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“SignalFx”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SignalFx”  。

    ![应用程序列表中的 SignalFx 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-signalfx-test-user"></a>创建 SignalFx 测试用户

本部分的目的是在 SignalFx 中创建名为 Britta Simon 的用户。 SignalFx 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 SignalFx 期间，如果尚不存在用户，则会创建一个新用户。

当用户首次通过 SAML SSO 登录到 SignalFx 时，[SignalFx 支持团队](mailto:kmazzola@signalfx.com)会向其发送一封电子邮件，其中包含他们必须单击以进行身份验证的链接。 这仅在用户首次登录时发生，后续登录尝试不要求进行电子邮件验证。

> [!Note]
> 如果需要手动创建用户，请联系  [SignalFx 支持团队](mailto:kmazzola@signalfx.com)

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“SignalFx”磁贴时，应会自动登录到设置了 SSO 的 SignalFx。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

