---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Google Cloud (G Suite) Connector 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Google Cloud (G Suite) Connector 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b025d98c230bc82b86a736785fb8e6581ec4519c
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864413"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Google Cloud (G Suite) Connector 的集成

本教程介绍如何将 Google Cloud (G Suite) Connector 与 Azure Active Directory (Azure AD) 集成。 将 Google Cloud (G Suite) Connector 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Google Cloud (G Suite) Connector。
* 让用户使用其 Azure AD 帐户自动登录到 Google Cloud (G Suite) Connector。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

- 一个 Azure AD 订阅。
- 已启用 Google Cloud (G Suite) Connector 单一登录 (SSO) 的订阅。
- Google Apps 订阅或 Google Cloud Platform 订阅。

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。 本文档中是使用新用户单一登录体验进行创建的。 如果你仍在使用旧版，安装程序看起来会有所不同。 可以在 G Suite 应用程序的单一登录设置中启用新体验。 转到“Azure AD，企业应用程序”，依次选择“Google Cloud (G Suite) Connector”、“单一登录”，然后单击“尝试新体验”。    

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="frequently-asked-questions"></a>常见问题

1. **问：此集成是否支持 Google Cloud Platform SSO 与 Azure AD 的集成？**

    A:是的。 Google Cloud Platform 和 Google Apps 共用同一个身份验证平台。 因此，若要实现 GCP 集成，需要配置 Google Apps 的 SSO。

2. **问：Chromebook 和其他 Chrome 设备与 Azure AD 单一登录兼容吗？**
  
    A:兼容，用户能够使用他们的 Azure AD 凭据登录到他们的 Chromebook 设备。 若要了解为何有时用户会两次收到输入凭据的提示，请参阅此 [Google Cloud (G Suite) Connector 支持文章](https://support.google.com/chrome/a/answer/6060880)。

3. **问：如果我启用单一登录，用户是否可以使用他们的 Azure AD 凭据登录到任何 Google 产品（例如 Google Classroom、GMail、Google Drive、YouTube 等）？**

    A:可以，具体取决于选择为组织启用或禁用的 [Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)。

4. **问：是否可以仅为一部分 Google Cloud (G Suite) Connector 用户启用单一登录？**

    A:不可以，启用单一登录会立即要求所有 Google Cloud (G Suite) Connector 用户使用其 Azure AD 凭据进行身份验证。 由于 Google Cloud (G Suite) Connector 不支持使用多个标识提供者，因此，Google Cloud (G Suite) Connector 环境的标识提供者可以是 Azure AD 或 Google - 但不能同时为两者。

5. **问：如果用户通过 Windows 登录，他们是否会自动进行 Google Cloud (G Suite) Connector 身份验证而不会收到输入密码的提示？**

    A:有两种用于启用此方案的选项。 第一种，用户可通过 [Azure Active Directory Join](../device-management-introduction.md) 登录到 Windows 10 设备。 或者，用户可以登录到通过域加入的方式加入到一个本地 Active Directory（已通加 [Active Directory 联合身份验证服务 (AD FS)](../hybrid/plan-connect-user-signin.md) 部署启用 Azure AD 单一登录）的 Windows 设备。 两种选项都要求执行以下教程中的步骤，才能在 Azure AD 与 Google Cloud (G Suite) Connector 之间启用单一登录。

6. **问：当我收到"无效的电子邮件"错误消息时，该怎么办？**

    A:对于此设置，需要电子邮件属性才能使用户进行登录。 无法手动设置此属性。

    对于任何拥有有效的 Exchange 许可证的用户，电子邮件属性会自动填充。 如果用户未启用电子邮件，则将收到此错误，因为应用程序需要获取此属性才能提供访问权限。

    你可以使用管理员帐户访问 portal.office.com，接着单击“管理中心”、“帐单”、“订阅”，选择你的 Office 365 订阅，然后单击“分配给用户”，选择要检查其订阅的用户，然后在右侧窗格中单击“编辑许可证”。

    分配 O365 许可证后，可能需要几分钟的时间才能应用。 之后，user.mail 属性将自动填充，此问题应会得到解决。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Google Cloud (G Suite) Connector 支持 **SP** 发起的 SSO

* Google Cloud (G Suite) Connector 支持[**自动**用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* 配置 Google Cloud (G Suite) Connector 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>从库中添加 Google Cloud (G Suite) Connector

若要配置 Google Cloud (G Suite) Connector 与 Azure AD 的集成，需要从库中将 Google Cloud (G Suite) Connector 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Google Cloud (G Suite) Connector**。 
1. 在结果面板中选择“Google Cloud (G Suite) Connector”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>配置并测试 Google Cloud (G Suite) Connector 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Google Cloud (G Suite) Connector 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Google Cloud (G Suite) Connector 中的相关用户之间建立链接关系。

若要配置并测试 Google Cloud (G Suite) Connector 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Google Cloud (G Suite) Connector SSO](#configure-google-cloud-g-suite-connector-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Google Cloud (G Suite) Connector 测试用户](#create-google-cloud-g-suite-connector-test-user)** - 在 Google Cloud (G Suite) Connector 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Google Cloud (G Suite) Connector”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，如果想要为 **Gmail** 进行配置，请执行以下步骤：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL  ：

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL： 

    | |
    |--|
    | `https://www.google.com` |
    | `https://www.google.com/a/<yourdomain.com>` |


1. 在“基本 SAML 配置”  部分中，如果想要为 **Google Cloud Platform** 进行配置，请执行以下步骤：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL  ：
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. 在“回复 URL”  文本框中，使用以下模式键入 URL： 
    
    | |
    |--|
    | `https://www.google.com` |
    | `https://www.google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 Google Cloud (G Suite) Connector 不会在单一登录配置上提供“实体 ID/标识符”值，因此，在取消选中“特定于域的颁发者”选项后，“标识符”值将为 `google.com`。  如果勾选“特定于域的颁发者”选项，  它将为 `google.com/a/<yourdomainname.com>`。 若要选中/取消选中“特定于域的颁发者”选项，需转到“配置 Google Cloud (G Suite) Connector SSO”部分进行操作，详见本教程稍后的介绍。   如需更多信息，请联系 [Google Cloud (G Suite) Connector 客户端支持团队](https://www.google.com/contact/)。

1. Google Cloud (G Suite) Connector 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示一个示例。 “唯一用户标识符”的默认值是“user.userprincipalname”，但 Google Cloud (G Suite) Connector 要求通过用户的电子邮件地址映射此项   。 为此，可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。

    ![image](common/default-attributes.png)


1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Google Cloud (G Suite) Connector”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Google Cloud (G Suite) Connector 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“Google Cloud (G Suite) Connector”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-google-cloud-g-suite-connector-sso"></a>配置 Google Cloud (G Suite) Connector SSO

1. 在浏览器中打开新的标签页，并使用管理员帐户登录到 [Google Cloud (G Suite) Connector 管理员控制台](https://admin.google.com/)。

2. 单击 **“安全性”** 。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”  菜单下。

    ![单击“安全”。][10]

3. 在“安全”  页上单击“设置单一登录 (SSO)”  。

    ![单击“SSO”。][11]

4. 执行以下配置更改：

    ![配置 SSO][12]

    a. 选择“使用第三方标识提供者设置 SSO”  。

    b. 在 Google Cloud (G Suite) Connector 中的“登录页 URL”字段内，粘贴从 Azure 门户复制的“登录 URL”值。  

    c. 在 Google Cloud (G Suite) Connector 中的“注销页 URL”字段内，粘贴从 Azure 门户复制的“注销 URL”值。  

    d. 在 Google Cloud (G Suite) Connector 中的“更改密码 URL”字段内，粘贴从 Azure 门户复制的“更改密码 URL”值。  

    e. 在 Google Cloud (G Suite) Connector 中，对于“验证证书”，请上传从 Azure 门户下载的证书。 

    f. 按照 Azure AD 中的上述“基本 SAML 配置”部分提供的说明，勾选/取消选中“使用特定于域的颁发者”选项。  

    g. 单击 **“保存更改”** 。

### <a name="create-google-cloud-g-suite-connector-test-user"></a>创建 Google Cloud (G Suite) Connector 测试用户

本部分的目标是[在 Google Cloud (G Suite) Connector 中创建名为 B.Simon 的用户](https://support.google.com/a/answer/33310?hl=en)。 在 Google Cloud (G Suite) Connector 中手动创建用户后，该用户现在可以使用其 Office 365 登录凭据登录。

Google Cloud (G Suite) Connector 还支持自动用户预配。 若要配置自动用户预配，必须先[为自动用户预配配置 Google Cloud (G Suite) Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)。

> [!NOTE]
> 如果在测试单一登录之前尚未在 Azure AD 中启用预配，请确保 Google Cloud (G Suite) Connector 中已存在用户。

> [!NOTE]
> 如果需要手动创建用户，请联系 [Google 支持团队](https://www.google.com/contact/)。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Google Cloud (G Suite) Connector”磁贴时，应会自动登录到设置了 SSO 的 Google Cloud (G Suite) Connector。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [在 Azure AD 中试用 Google Cloud (G Suite) Connector](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Google Cloud (G Suite) Connector](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png