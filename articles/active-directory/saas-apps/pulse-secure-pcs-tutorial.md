---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Pulse Secure PCS 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Pulse Secure PCS 之间配置单一登录。
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
ms.openlocfilehash: c46565f7eaa060a060183cf321bb1df1903b945c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992161"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Pulse Secure PCS 集成

本教程介绍如何将 Pulse Secure PCS 与 Azure Active Directory (Azure AD) 集成。 将 Pulse Secure PCS 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Pulse Secure PCS。
* 让用户使用其 Azure AD 帐户自动登录到 Pulse Secure PCS。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 支持 Pulse Secure PCS 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Pulse Secure PCS 支持 SP 发起的 SSO

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>从库中添加 Pulse Secure PCS

要配置 Pulse Secure PCS 与 Azure AD 的集成，需要从库中将 Pulse Secure PCS 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Pulse Secure PCS” 。
1. 在结果面板中选择“Pulse Secure PCS”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>配置并测试 Pulse Secure PCS 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Pulse Secure PCS 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Pulse Secure PCS 相关用户之间建立链接关系。

若要配置并测试 Pulse Secure PCS 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Pulse Secure PCS SSO](#configure-pulse-secure-pcs-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Pulse Secure PCS 测试用户](#create-pulse-secure-pcs-test-user)** - 在 Pulse Secure PCS 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“Pulse Secure PCS”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、回复 URL 和标识符更新这些值。 请联系 [Pulse Secure PCS 客户端支持团队](mailto:support@pulsesecure.net)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Pulse Secure PCS”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Pulse Secure PCS 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Pulse Secure PCS”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-pulse-secure-pcs-sso"></a>配置 Pulse Secure PCS SSO

本部分介绍将 PCS 配置为 SAML SP 时所需的 SAML 配置。 其他基本配置（如创建领域和角色）均未涵盖。

**Pulse Connect Secure 配置包括：**

* 将 Azure AD 配置为 SAML 元数据提供程序
* 配置 SAML 身份验证服务器
* 分配给各自的领域和角色

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>将 Azure AD 配置为 SAML 元数据提供程序

在下面的页中执行以下步骤：

![Pulse Connect Secure 配置](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. 登录到 Pulse Connect Secure 管理控制台
1. 导航到“系统”->“配置”->“SAML”
1. 单击“新元数据提供程序”
1. 在“名称”文本框中提供有效名称
1. 将下载的元数据 XML 文件从 Azure 门户上传到“Azure AD 元数据文件”。
1. 选择“接受未签名的元数据”
1. 对于“标识提供者”，选择“角色”
1. 单击“保存更改”。

#### <a name="steps-to-create-a-saml-auth-server"></a>创建 SAML 身份验证服务器的步骤：

1. 导航到“身份验证”->“身份验证服务器”
1. 选择“新建 **：SAML 服务器”然后单击“新建服务器”** 

    ![Pulse Connect Secure 身份验证服务器](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. 在“设置”页上，执行下列步骤：

    ![Pulse Connect Secure 身份验证服务器设置](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. 在文本框中提供“服务器名称”。

    b. 选择“SAML 版本 2.0”，然后为“配置模式”选择“元数据”  。

    c. 复制“Connect Secure 实体 ID”值，将其粘贴到 Azure 门户的“基本 SAML 配置”对话框中的“标识符 URL”框中  。

    d. 从“标识提供者实体 ID 下拉列表”中选择“Azure AD 实体 ID”值。

    e. 从“标识提供者单一登录服务 URL 下拉列表”中选择“Azure AD 登录 URL”值。

    f. “单一注销”是可选设置。 如果选择此选项，则注销后会提示进行新的身份验证。 如果未选择此选项，并且尚未关闭浏览器，则无需身份验证即可重新连接。

    g. 为“请求的身份验证上下文类”选择“密码”，为“比较方法”选择“完全匹配”   。

    h.如果该值不存在，请单击“添加行”。 设置“元数据有效性”，以天数表式。
    
    i. 单击“保存更改”

### <a name="create-pulse-secure-pcs-test-user"></a>创建 Pulse Secure PCS 测试用户

在本部分中，在 Pulse Secure PCS 中创建名为 Britta Simon 的用户。 请与  [Pulse Secure PCS 支持团队](mailto:support@pulsesecure.net)协作，将用户添加到 Pulse Secure PCS 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

1. 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Pulse Secure PCS 登录 URL，可以在其中启动登录流。 

2. 直接转到 Pulse Secure PCS 登录 URL，并在其中启动登录流。

3. 可以使用 Microsoft 访问面板。 如果在访问面板中单击 Pulse Secure PCS 磁贴，这会重定向到 Pulse Secure PCS 登录 URL。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="next-steps"></a>后续步骤

配置 Pulse Secure PCS 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。


