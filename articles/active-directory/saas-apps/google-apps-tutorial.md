---
title: 教程：Azure Active Directory 与 G Suite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 G Suite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2018
ms.author: jeedes
ms.openlocfilehash: bcea7848c7331ecd326f0ccb6ab9f543ce972205
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834683"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>教程：Azure Active Directory 与 G Suite 集成

本教程介绍如何将 G Suite 与 Azure Active Directory (Azure AD) 集成。

将 G Suite 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 G Suite。
- 可让用户使用其 Azure AD 帐户自动登录到 G Suite（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 G Suite 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 G Suite 单一登录的订阅
- Google Apps 订阅或 Google Cloud Platform 订阅。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。 本文档中是使用新用户单一登录体验进行创建的。 如果你仍在使用旧版，安装程序看起来会有所不同。 可以在 G Suite 应用程序的单一登录设置中启用新体验。 转到“Azure AD，企业应用程序”，依次选择“G Suite”、“单一登录”，然后单击“尝试新体验”。

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

6. **错误：电子邮件无效**

    对于此设置，需要电子邮件属性才能使用户进行登录。 无法手动设置此属性。

    对于任何拥有有效的 Exchange 许可证的用户，电子邮件属性会自动填充。 如果用户未启用电子邮件，则将收到此错误，因为应用程序需要获取此属性才能提供访问权限。

    你可以使用管理员帐户访问 portal.office.com，接着单击“管理中心”、“帐单”、“订阅”，选择你的 Office 365 订阅，然后单击“分配给用户”，选择要检查其订阅的用户，然后在右侧窗格中单击“编辑许可证”。

    分配 O365 许可证后，可能需要几分钟的时间才能应用。 之后，user.mail 属性将自动填充，此问题应会得到解决。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 G Suite
2. 配置和测试 Azure AD 单一登录

## <a name="adding-g-suite-from-the-gallery"></a>从库添加 G Suite

若要配置 G Suite 与 Azure AD 的集成，需要从库中将 G Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 G Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“G Suite”，在结果面板中选择“G Suite”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 G Suite](./media/google-apps-tutorial/tutorial_gsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 G Suite 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 G Suite 用户。 换句话说，需要建立 Azure AD 用户与 G Suite 中相关用户之间的链接关系。

若要配置和测试 G Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 G Suite 测试用户](#creating-a-g-suite-test-user) - 在 G Suite 中创建与用户的 Azure AD 表示形式相关联的 Britta Simon 的对应用户**。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 G Suite 应用程序中配置单一登录。

**若要配置 G Suite 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **G Suite** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![G Suite 域和 URL 单一登录信息](./media/google-apps-tutorial/tutorial_gsuite_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [G Suite 客户端支持团队](https://www.google.com/contact/)获取这些值。

5. G Suite 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](./media/google-apps-tutorial/i3-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“编辑”按钮，以打开“管理用户声明”对话框。

    ![图像](./media/google-apps-tutorial/i2-attribute.png)

    ![图像](./media/google-apps-tutorial/i4-attribute.png)

    b. 在“源属性”列表中，选择属性值。

    c. 单击“ **保存**”。

5. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/google-apps-tutorial/tutorial_gsuite_certificate.png) 

6. 在“设置 G Suite”部分中，根据要求复制相应的 URL。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![G Suite 配置](common/configuresection.png)

9. 在浏览器中打开新选项卡并使用管理员帐户登录到 [G Suite 管理员控制台](https://admin.google.com/)。

10. 单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。

    ![单击“安全”。][10]

11. 在“安全”页上单击“设置单一登录 (SSO)”。

    ![单击“SSO”。][11]

12. 执行以下配置更改：

    ![配置 SSO][12]

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“使用第三方标识提供者设置 SSO”。

    b. 在 G Suite 中的“登录页 URL”字段中，粘贴从 Azure 门户复制的“登录 URL”的值 **。** 

    c. 在 G Suite 中的“注销页 URL”字段中，粘贴从 Azure 门户复制的“注销 URL”的值 **。** 

    d. 在 G Suite 中的“更改密码 URL”字段中，粘贴从 Azure 门户复制的“更改密码 URL”的值 **。** 

    e. 在 G Suite 中，为“验证证书”上传从 Azure 门户下载的证书。

    f. 选择“使用域特定的颁发者”。

    g. 单击“保存更改”。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-g-suite-test-user"></a>创建 G Suite 测试用户

本部分的目的是在 G Suite 中创建名为“Britta Simon”的用户。 G Suite 支持在默认情况下启用的自动预配。 在此部分中无需进行任何操作。 尝试访问 G Suite 时，如果 G Suite 中没有用户，系统会创建一个新用户。

> [!NOTE]
> 如果在测试单一登录前尚未启用 Azure AD 中的预配，请确保用户已存在于 G Suite 中。

> [!NOTE]
> 如果需要手动创建用户，请联系 [Google 支持团队](https://www.google.com/contact/)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 G Suite 的访问权限，使其可以使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“G Suite”。

    ![配置单一登录](./media/google-apps-tutorial/tutorial_gsuite_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“G Suite”磁贴时，应会自动登录到 G Suite 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png