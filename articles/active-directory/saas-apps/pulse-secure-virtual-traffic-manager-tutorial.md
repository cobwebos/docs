---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Pulse Secure Virtual Traffic Manager 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Pulse Secure Virtual Traffic Manager 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d487295e9aab1a56553dc9d31b0a8714688005c3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91337745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Pulse Secure Virtual Traffic Manager 的集成

本教程介绍如何将 Pulse Secure Virtual Traffic Manager 与 Azure Active Directory (Azure AD) 集成。 将 Pulse Secure Virtual Traffic Manager 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Pulse Secure Virtual Traffic Manager。
* 让用户使用其 Azure AD 帐户自动登录到 Pulse Secure Virtual Traffic Manager。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Pulse Secure Virtual Traffic Manager 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Pulse Secure Virtual Traffic Manager 支持 **SP** 发起的 SSO

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>从库中添加 Pulse Secure Virtual Traffic Manager

若要配置 Pulse Secure Virtual Traffic Manager 与 Azure AD 的集成，需要从库中将 Pulse Secure Virtual Traffic Manager 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中键入“Pulse Secure Virtual Traffic Manager” 。
1. 在结果面板中选择“Pulse Secure Virtual Traffic Manager”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>为 Pulse Secure Virtual Traffic Manager 配置并测试 Azure AD SSO

使用名为 B.Simon 的测试用户为 Pulse Secure Virtual Traffic Manager 配置并测试 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Pulse Secure Virtual Traffic Manager 相关用户之间建立关联。

若要为 Pulse Secure Virtual Traffic Manager 配置并测试 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Pulse Secure Virtual Traffic Manager SSO](#configure-pulse-secure-virtual-traffic-manager-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Pulse Secure Virtual Traffic Manager 测试用户](#create-pulse-secure-virtual-traffic-manager-test-user)** - 在 Pulse Secure Virtual Traffic Manager 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Pulse Secure Virtual Traffic Manager”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<published virtual server FQDN>/saml/consume`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<published virtual server FQDN>/saml/metadata`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Pulse Secure Virtual Traffic Manager 客户端支持团队](mailto:support@pulsesecure.net)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Pulse Secure Virtual Traffic Manager”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 B.Simon 访问 Pulse Secure Virtual Traffic Manager 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Pulse Secure Virtual Traffic Manager”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>配置 Pulse Secure Virtual Traffic Manager SSO

本部分介绍了在 Pulse Virtual Traffic Manager 上启用 Azure AD SAML 身份验证所需的配置。 所有配置更改都是使用管理 Web UI 在 Pulse Virtual Traffic Manager 上进行的。 

#### <a name="create-a-saml-trusted-identity-provider"></a>创建 SAML 受信任的标识提供者

a. 转到“Pulse Virtual Traffic Manager 设备管理 UI”>“目录”>“SAML”>“受信任的标识提供者目录”页，然后单击“编辑”。

![saml 目录页](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. 添加新的 SAML 受信任标识提供者的详细信息，从“单一登录设置”页面下的 Azure AD Enterprise 应用程序复制信息，然后单击“创建新的受信任的标识提供者”。

![创建新的受信任的标识提供者](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* 在“名称”文本框中，输入受信任的标识提供者的名称。 

* 在“Entity_id”文本框中，输入从 Azure 门户复制的“Azure AD 标识符”值 。  

* 在“URL”文本框中，输入从 Azure 门户复制的“登录 URL”值 。 

* 打开从 Azure 门户下载到记事本中的**证书**，将内容粘贴到“证书”文本框中。

c. 验证是否已成功创建新的 SAML 标识提供者。 

![验证受信任的标识提供者](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>将虚拟服务器配置为使用 Azure AD 身份验证

a. 转到“Pulse Virtual Traffic Manager 设备管理 UI”>“服务”>“虚拟服务器”页面，单击之前创建的虚拟服务器旁边的“编辑”。 

![虚拟服务器编辑](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. 在“身份验证”部分中，单击“编辑”。 

![“身份验证”部分](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. 为虚拟服务器配置以下身份验证设置： 

1. 身份验证 -

    ![虚拟服务器的身份验证设置](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. 在“Auth!type”中，选择“SAML 服务提供者” 

    b. 在“Auth!verbose”中，若要排查任何身份验证问题，请将其设置为“是”，否则请保留默认值“否” 

2. 身份验证会话管理 -

    ![身份验证会话管理](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. 对于“Auth!session!cookie_name”，请保留默认值“VS_SamlSP_Auth” 

    b. 对于“auth!session!timeout”，请保留默认值“7200” 

    c. 在“auth!session!log_external_state”中，若要排查任何身份验证问题，请将其设置为“是”，否则请保留默认值“否” 

    d. 在“auth!session!cookie_attributes”中，将其更改为“HTTPOnly” 

3. SAML 服务提供程序 -

    ![SAML 服务提供程序](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. 在“auth!saml!sp_entity_id”文本框中，将其设置为用作 Azure AD 单一登录配置标识符（实体 ID）的 URL。 例如 `https://pulseweb.labb.info/saml/metadata`。 

    b. 在“auth!saml!sp_acs_url”中，将其设置为用作 Azure AD 单一登录配置重播 URL（断言使用者服务 URL）的 URL。 例如 `https://pulseweb.labb.info/saml/consume`。 

    c. 在“auth!saml!idp”中，选择你在上一步创建的“受信任的标识提供者”。 

    d. 在“auth!saml!time_tolerance”中，保留默认值“5”秒。 

    e. 在“auth!saml!nameid_format”中，选择“未指定”。

    f. 通过单击页面底部的“更新”来应用更改。
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>创建 Pulse Secure Virtual Traffic Manager 测试用户

在本部分，我们在 Pulse Secure Virtual Traffic Manager 中创建名为 Britta Simon 的用户。 与 [Pulse Secure Virtual Traffic Manager 支持团队](mailto:support@pulsesecure.net)合作，在 Pulse Secure Virtual Traffic Manager 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

1. 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Pulse Secure Virtual Traffic Manager 登录 URL，你可以在其中启动登录流。 

2. 直接转到 Pulse Secure Virtual Traffic Manager 登录 URL，并在其中启动登录流。

3. 可以使用 Microsoft 访问面板。 如果在访问面板中单击 Pulse Secure Virtual Traffic Manager 磁贴，则会重定向到 Pulse Secure Virtual Traffic Manager 登录 URL。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 Pulse Secure Virtual Traffic Manager 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。