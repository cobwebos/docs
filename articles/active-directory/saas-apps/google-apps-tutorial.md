---
title: 教程：Azure Active Directory 与 G Suite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 G Suite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8196576c4b419fdfc8f7e3adf5c474ef128ebbf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101630"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>教程：Azure Active Directory 与 G Suite 集成

本教程介绍如何将 G Suite 与 Azure Active Directory (Azure AD) 集成。
将 G Suite 与 Azure AD 集成提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 G Suite。
* 可让用户使用其 Azure AD 帐户自动登录到 G Suite（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 G Suite 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 G Suite 单一登录的订阅
- Google Apps 订阅或 Google Cloud Platform 订阅。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。 本文档中是使用新用户单一登录体验进行创建的。 如果你仍在使用旧版，安装程序看起来会有所不同。 可以在 G Suite 应用程序的单一登录设置中启用新体验。 转到“Azure AD，企业应用程序”，依次选择“G Suite”、“单一登录”，然后单击“尝试新体验”     。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="frequently-asked-questions"></a>常见问题

1. **问：此集成是否支持 Google Cloud Platform SSO 与 Azure AD 的集成？**

    答：是的。 Google Cloud Platform 和 Google Apps 共用同一个身份验证平台。 因此，若要实现 GCP 集成，需要配置 Google Apps 的 SSO。

2. **问：Chromebook 和其他 Chrome 设备与 Azure AD 单一登录兼容吗？**
  
    答：兼容，用户能够使用他们的 Azure AD 凭据登录到他们的 Chromebook 设备。 若要了解为何有时用户会两次收到输入凭据的提示，请参阅此 [G Suite 支持文章](https://support.google.com/chrome/a/answer/6060880)。

3. **问：如果我启用单一登录，用户可以使用他们的 Azure AD 凭据登录到任何 Google 产品（例如 Google Classroom、GMail、Google Drive、YouTube 等）吗？**

    答：可以，具体取决于你选择为组织启用或禁用的 [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)。

4. **问：我能否仅为 G Suite 用户的一个子集启用单一登录？**

    答：不能，启用单一登录会立即要求所有 G Suite 用户使用他们的 Azure AD 凭据进行身份验证。 由于 G Suite 不支持具有多个标识提供者，因此，G Suite 环境的标识提供者可以是 Azure AD 或 Google - 但不能同时为两者。

5. **问：如果用户通过 Windows 登录，他们是否会自动进行 G Suite 身份验证而不会收到输入密码的提示？**

    答：有两种用于启用此方案的选项。 第一种，用户可通过 [Azure Active Directory Join](../device-management-introduction.md) 登录到 Windows 10 设备。 或者，用户可以登录到通过域加入的方式加入到一个本地 Active Directory（已通加 [Active Directory 联合身份验证服务 (AD FS)](../hybrid/plan-connect-user-signin.md) 部署启用 Azure AD 单一登录）的 Windows 设备。 两种选项都要求执行以下教程中的步骤，以在 Azure AD 和 G Suite 之间启用单一登录。

6. **问：当我收到"无效的电子邮件"错误消息时，该怎么办？**

    答：对于此设置，需要电子邮件属性才能使用户进行登录。 无法手动设置此属性。

    对于任何拥有有效的 Exchange 许可证的用户，电子邮件属性会自动填充。 如果用户未启用电子邮件，则将收到此错误，因为应用程序需要获取此属性才能提供访问权限。

    你可以使用管理员帐户访问 portal.office.com，接着单击“管理中心”、“帐单”、“订阅”，选择你的 Office 365 订阅，然后单击“分配给用户”，选择要检查其订阅的用户，然后在右侧窗格中单击“编辑许可证”。

    分配 O365 许可证后，可能需要几分钟的时间才能应用。 之后，user.mail 属性将自动填充，此问题应会得到解决。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* G Suite 支持“SP”发起的 SSO 
* G Suite 支持“[自动用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)” 

## <a name="adding-g-suite-from-the-gallery"></a>从库添加 G Suite

若要配置 G Suite 与 Azure AD 的集成，需要从库中将 G Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 G Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“G Suite”，在结果面板中选择“G Suite”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 G Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 G Suite 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 G Suite 相关用户之间建立链接关系。

若要配置和测试 G Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 G Suite 单一登录](#configure-g-suite-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 G Suite 测试用户](#create-g-suite-test-user)** - 在 G Suite 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 G Suite 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“G Suite”  应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，如果想要为 **Gmail** 进行配置，请执行以下步骤：

    ![G Suite 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL  ：

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [G Suite 客户端支持团队](https://www.google.com/contact/)获取这些值。

5. 在“基本 SAML 配置”  部分中，如果想要为 **Google Cloud Platform** 进行配置，请执行以下步骤：

    ![G Suite 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL  ：
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [G Suite 客户端支持团队](https://www.google.com/contact/)获取这些值。

6. G Suite 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示一个示例。 “唯一用户标识符”的默认值是“user.userprincipalname”，但 G Suite 要求通过用户的电子邮件地址映射此项   。 为此，可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。

    ![image](common/edit-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤     ：

    | 名称 | 源属性 |
    | ---------------| --------------- |
    | 唯一用户标识符 | User.mail |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

9. 在“设置 G Suite”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-g-suite-single-sign-on"></a>配置 G Suite 单一登录

1. 在浏览器中打开新选项卡并使用管理员帐户登录到 [G Suite 管理员控制台](https://admin.google.com/)。

2. 单击“安全”  。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”  菜单下。

    ![单击“安全”。][10]

3. 在“安全”  页上单击“设置单一登录 (SSO)”  。

    ![单击“SSO”。][11]

4. 执行以下配置更改：

    ![配置 SSO][12]

    a. 选择“使用第三方标识提供者设置 SSO”  。

    b. 在 G Suite 中的“登录页 URL”  字段中，粘贴从 Azure 门户复制的“登录 URL”的值。 

    c. 在 G Suite 中的“注销页 URL”  字段中，粘贴从 Azure 门户复制的“注销 URL”的值。 

    d. 在 G Suite 中的“更改密码 URL”  字段中，粘贴从 Azure 门户复制的“更改密码 URL”  的值。

    e. 在 G Suite 中，为“验证证书”  上传从 Azure 门户下载的证书。

    f. 选择“使用域特定的颁发者”。 

    g. 单击“保存更改”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension    
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 G Suite 的访问权限，使其可以使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“G Suite”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“G Suite”。 

    ![应用程序列表中的 G Suite 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-g-suite-test-user"></a>创建 G Suite 测试用户

本部分的目的是在 G Suite 中创建名为“Britta Simon”的用户。 G Suite 支持在默认情况下启用的自动预配。 在此部分中无需进行任何操作。 尝试访问 G Suite 时，如果 G Suite 中没有用户，系统会创建一个新用户。

> [!NOTE]
> 如果在测试单一登录前尚未启用 Azure AD 中的预配，请确保用户已存在于 G Suite 中。

> [!NOTE]
> 如果需要手动创建用户，请联系 [Google 支持团队](https://www.google.com/contact/)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 G Suite 磁贴时，应当会自动登录到为其设置了 SSO 的 G Suite。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [配置用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
